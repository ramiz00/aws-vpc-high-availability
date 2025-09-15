# AWS VPC High Availability Setup

This repository contains a step-by-step guide to setting up a secure and highly available AWS Virtual Private Cloud (VPC) architecture using public and private subnets, NAT Gateway, Bastion Host, Application Load Balancer (ALB), and VPC Peering. The guide includes diagrams, screenshots, and cost optimization tips to help cloud engineers and DevOps enthusiasts practice real-world AWS networking.

---

## 📌 Features

✔ Create VPC with 2 public and 2 private subnets across multiple availability zones  
✔ Configure Internet Gateway and NAT Gateway for routing  
✔ Secure access to private instances using Bastion Host  
✔ Deploy a website inside a private subnet using ALB  
✔ Set up VPC Peering between different regions  
✔ Cost optimization tips for NAT Gateway, Elastic IPs, and tenancy options  
✔ Diagrams and commands for easy learning  

---

## 🛠 Setup Overview

1. Create VPC with CIDR block `172.20.0.0/16`  
2. Launch public and private subnets across availability zones  
3. Configure route tables for IGW and NAT Gateway  
4. Set up Bastion Host with restricted SSH access  
5. Install and deploy a sample website in a private EC2 instance  
6. Create Application Load Balancer to expose the website securely  
7. Set up VPC Peering across regions and update routes  
8. Apply best practices for cost optimization  

---

## 📸 Diagrams

### VPC Architecture  
![VPC Architecture](images/vpc-architecture.png)

### VPC Subnets  
![VPC Subnet](images/vpc-subnet.png)

### Security Group  
![Security Group](images/sec-grp.png)

### Instances / Bastion  
![Instance](images/instance.png)

### Application Load Balancer (ALB)  
![ALB](images/alb.png)

### Website (live screenshot)  
![Website Live](images/website-ss-live.png)

### VPC Peering (primary)  
![VPC Peering](images/vpc-peering.png)

### VPC Peering (Oregon / cross-region)  
![VPC Peering Oregon](images/vpc-peering-oregon.png)

---

## 📂 Folder Structure

aws-vpc-high-availability/
├── README.md  
├── vpc-setup.md  
├── bastion-host.md  
├── website-deployment.md  
├── vpc-peering.md  
├── cost-optimization.md  
└── images/  
    ├── alb.png  
    ├── instance.png  
    ├── sec-grp.png  
    ├── vpc-architecture.png  
    ├── vpc-peering-oregon.png  
    ├── vpc-peering.png  
    ├── vpc-subnet.png  
    └── website-ss-live.png


---
## ✅ Recommended reading order (follow this guide)

Follow these files in order for a smooth hands‑on lab:

1. vpc-setup.md — Build the VPC, create subnets, IGW, route tables, and NAT Gateway.  
2. bastion-host.md — Configure the bastion (jump) host and learn the SSH workflow.  
3. website-deployment.md — Deploy the web server in private subnets, create ALB and target group, and configure security groups.  
4. vpc-peering.md — Set up VPC peering and update route tables for cross‑VPC connectivity.  
5. cost-optimization.md — Review cost-saving options and cleanup steps.  
6. images/ — Open the diagrams referenced in each guide while reading.

Tip: Read vpc-setup.md fully before launching resources; use bastion-host.md during website deployment.

## 💡 Technologies Used

- AWS VPC  
- Internet Gateway & NAT Gateway  
- EC2, Security Groups, Key Pairs  
- Application Load Balancer (ALB)  
- VPC Peering  
- Apache Web Server  

---

## 📜 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 📬 Contact

Connect with me on [LinkedIn](https://www.linkedin.com/in/ramiz-raja-951829169/)  
Check out the full tutorial and diagrams here: [GitHub Repository](https://github.com/ramiz00/aws-vpc-high-availability)

---
Happy Learning! 🚀


