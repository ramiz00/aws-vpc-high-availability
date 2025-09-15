# VPC Peering – Connect Multiple VPCs Securely

This guide shows how to connect two VPCs (same or different regions/accounts) using VPC Peering.

---

## Prerequisites
- Non-overlapping CIDR blocks (example: 172.20.0.0/16 and 172.22.0.0/16).  
- VPC IDs and Route Table IDs for both VPCs.  
- Permissions in both accounts/regions if peering across accounts/regions.

---

## Step 1 — Create the second VPC (example)
- Region: us-west-2  
- Name: vprof-db  
- CIDR: 172.22.0.0/16  
- Create required subnets and a route table (vprof-db-rt).

---

## Step 2 — Create Peering Connection (Console)
1. VPC → Peering Connections → Create Peering Connection.  
2. Name: vprof-nc-peering. Requester: vprofile-vpc. Accepter: vprof-db (select region/account).  
3. Send request and accept it from the accepter account/region.

Optional AWS CLI:
Requester:
```bash
aws ec2 create-vpc-peering-connection \
  --vpc-id vpc-REQUESTER_ID \
  --peer-vpc-id vpc-ACCEPTER_ID \
  --peer-region us-west-2 \
  --tag-specifications 'ResourceType=vpc-peering-connection,Tags=[{Key=Name,Value=vprof-nc-peering}]'
```
Accepter (accept):
```bash
aws ec2 accept-vpc-peering-connection --vpc-peering-connection-id pcx-XXXXXXXX
```

---

## Step 3 — Update Route Tables (both VPCs)
- In vprofile-vpc route table(s): add route
  - Destination: 172.22.0.0/16 → Target: Peering Connection (pcx-...)
- In vprof-db route table(s): add route
  - Destination: 172.20.0.0/16 → Target: Peering Connection (pcx-...)

AWS CLI example:
```bash
aws ec2 create-route --route-table-id rtb-REQUESTER_RTID --destination-cidr-block 172.22.0.0/16 --vpc-peering-connection-id pcx-XXXXXXXX
aws ec2 create-route --route-table-id rtb-ACCEPTER_RTID --destination-cidr-block 172.20.0.0/16 --vpc-peering-connection-id pcx-XXXXXXXX
```

Notes:
- Apply routes to the route tables associated with the subnets that need cross-VPC access.  
- Do NOT add routes to an Internet Gateway for peering traffic.

---

## Step 4 — Update Security Groups
- Allow inbound traffic from the remote VPC CIDR (e.g., 172.22.0.0/16) on required ports (SSH, HTTP, DB).  
- Prefer narrow rules (specific ports and sources) rather than wide CIDRs.

---

## Troubleshooting & Notes
- Peering is not transitive; use Transit Gateway for multi-VPC connectivity at scale.  
- Check Network ACLs, SGs, and routes if connectivity fails.  
- For cross-region peering, consider latency and potential data transfer costs.

---

## Cleanup
1. Remove peering routes from route tables.  
2. Delete the VPC Peering Connection.  
3. Clean up unused route tables/subnets/VPCs