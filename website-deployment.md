# Website Deployment in Private Subnet — Using ALB

This guide shows how to deploy a website on a private EC2 instance and expose it via an internet-facing Application Load Balancer (ALB).

---

## Prerequisites
- VPC and subnets created (see `vpc-setup.md`).  
- Bastion host deployed and reachable (see `bastion-host.md`).  
- Key pairs: `vprof-bastion-kp.pem` (bastion) and `web-key.pem` (web instance).  
- Reference names used: VPC `vprofile-vpc`, public subnets `public-sub-1a/1b`, private subnets `private-sub-1a/1b`. Adjust to your env.

---

## Step 1 — Launch EC2 in Private Subnet
1. EC2 → Launch Instance.  
2. Choose Amazon Linux 2 or Ubuntu AMI.  
3. Name: `web-server-1`.  
4. Key pair: `web-key.pem`. (Do NOT commit keys to repo.)  
5. Network: select `vprofile-vpc` → Subnet: `private-sub-1a` (no public IP).  
6. Security group: attach `web-sg` (allow HTTP from ALB SG only; block SSH from internet).

Suggested SG rules:
- alb-sg: inbound HTTP (80) from 0.0.0.0/0 (or restricted CIDRs).  
- web-sg: inbound HTTP (80) from `alb-sg` only; no SSH from internet.

---

## Step 2 — Connect via Bastion Host
From workstation to bastion:
```bash
ssh -i vprof-bastion-kp.pem ubuntu@<BASTION_PUBLIC_IP>   # ubuntu AMI
# or
ssh -i vprof-bastion-kp.pem ec2-user@<BASTION_PUBLIC_IP> # Amazon Linux bastion
```

Copy web key to bastion (if needed):
```bash
scp -i vprof-bastion-kp.pem web-key.pem ubuntu@<BASTION_PUBLIC_IP>:/home/ubuntu/
```

From bastion to private instance:
```bash
# Amazon Linux target
ssh -i web-key.pem ec2-user@172.20.3.98

# Ubuntu target
ssh -i web-key.pem ubuntu@172.20.3.98
```

Or use ProxyCommand from your workstation (no copying keys):
```bash
scp -o "ProxyCommand ssh -i vprof-bastion-kp.pem -W %h:%p ubuntu@<BASTION_PUBLIC_IP>" -i web-key.pem web-content.zip ec2-user@172.20.3.98:/home/ec2-user/
```

---

## Step 3 — Install Web Server
Amazon Linux 2:
```bash
sudo yum update -y
sudo yum install httpd -y
sudo systemctl enable --now httpd
```

Ubuntu:
```bash
sudo apt update -y
sudo apt install apache2 -y
sudo systemctl enable --now apache2
```

---

## Step 4 — Deploy Website Content (example)
```bash
# Install utilities
# Amazon Linux
sudo yum install wget unzip -y
# Ubuntu
sudo apt install wget unzip -y

# Download sample and copy to web root
wget https://www.tooplate.com/zip-templates/2136_kool_form_pack.zip
unzip 2136_kool_form_pack.zip
sudo cp -r 2136_kool_form_pack/* /var/www/html/
# Restart web service
sudo systemctl restart httpd    # Amazon Linux
sudo systemctl restart apache2  # Ubuntu
```

Health-check path: use `/` or `/index.html`.

---

## Step 5 — Create Target Group & Register Targets
1. EC2 → Target Groups → Create target group.  
   - Target type: Instance  
   - Protocol: HTTP, Port: 80  
   - VPC: `vprofile-vpc`  
   - Health check path: `/`  
2. Register `web-server-1` (private instance). Confirm target becomes healthy.

---

## Step 6 — Create ALB
1. EC2 → Load Balancers → Create → Application Load Balancer.  
2. Name: `vprof-web-elb`. Scheme: internet-facing. VPC: `vprofile-vpc`. Subnets: `public-sub-1a`, `public-sub-1b`.  
3. Security group: `alb-sg` (allow inbound 80).  
4. Listener: HTTP:80 → forward to the target group from Step 5.  
5. Create and verify target health.

For production, terminate on HTTPS (443) with ACM certs.

---

## Step 7 — Test
- Open ALB DNS in browser or:
```bash
curl -I http://<ALB_DNS_NAME>
curl http://<ALB_DNS_NAME>/
```
Expect HTTP 200 and HTML content.

---

## Notes & Best Practices
- Instance username: Amazon Linux → ec2-user; Ubuntu → ubuntu.  
- Private instances must NOT have public IPs; use bastion for SSH.  
- AWS reserves 5 IPs per subnet (.0, .1, .2, .3, .255); usable .4–.254 in /24.  
- NAT Gateway is per-AZ and billed hourly + data; use VPC endpoint for S3 to reduce egress.  
- Do not commit `.pem` files. Rotate keys if exposed.  
- Ensure `web-sg` allows traffic only from `alb-sg`. ALB must reach instance on port 80 for health checks.

---

## Troubleshooting
- Target unhealthy: check web server status, SGs, and health-check path.  
- 502/504 from ALB: verify backend response, security groups, and NAT/route settings for private instances.  
- SSH failure to private instance: verify correct key, username, and bastion jump configuration.

---

## Cleanup (recommended order)
1. Delete ALB → listeners → target group.  
2. Terminate EC2 instances.  
3. Delete NAT Gateways → release EIPs.  
4. Detach & delete Internet Gateway.