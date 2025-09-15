# AWS Cost Optimization Tips – Save Money on VPC Setup

When designing your AWS network, optimize cost without compromising security or required availability.

---

## Principles
- Right-size first: choose appropriate instance types for actual load.  
- Prefer managed, serverless, or purpose-built services (S3, RDS, Lambda) to reduce operational cost.  
- Trade off HA vs cost: NAT per-AZ improves availability; a single NAT can reduce cost for labs.

---

## Recommended actions

1. NAT Gateways
   - For production: deploy one NAT Gateway per AZ for high availability.  
   - For labs/dev: consider one NAT Gateway (single AZ) or a NAT instance to reduce cost.  
   - Monitor NAT egress and optimize (use VPC endpoints for S3 to avoid NAT egress).

2. VPC Endpoints
   - Use Gateway VPC Endpoint for S3 and DynamoDB to avoid NAT egress charges:
     - CLI example (S3 gateway endpoint):
       aws ec2 create-vpc-endpoint --vpc-id vpc-XXXXXXXX --service-name com.amazonaws.<region>.s3 --route-table-ids rtb-YYYYYYYY --policy-document file://policy.json --region <region>

3. Elastic IPs (EIP)
   - Release unused EIPs — they incur charges when allocated but unused.
     - CLI release example:
       aws ec2 release-address --allocation-id eipalloc-XXXXXXXX

4. Idle/Unused Resources
   - Delete unused snapshots, volumes, unattached ENIs, ELBs, and NAT Gateways.  
   - Terminate or downsize underutilized instances; use instance schedules for non-prod.

5. Compute Savings
   - Use Savings Plans or Reserved Instances for predictable workloads.  
   - Use Spot Instances for fault-tolerant or batch workloads.  
   - Use auto-scaling with appropriate scale-in protection and lifecycle policies.

6. Storage & Data Transfer
   - Use lifecycle rules on S3 to move infrequently accessed data to cheaper tiers.  
   - Avoid cross-AZ/region traffic where possible; monitor data transfer costs.

7. Monitoring, Tagging & Budgets
   - Enable Cost Allocation Tags and tag all resources (env, owner, cost-center).  
   - Create AWS Budgets and billing alerts.  
   - Use Cost Explorer and AWS Trusted Advisor to find savings.

8. Logging & Diagnostics (low cost)
   - Enable VPC Flow Logs selectively (filtered) to troubleshoot traffic bills.  
   - Use CloudWatch metrics & dashboards to detect abnormal usage.

---

## Quick cleanup & CLI examples

- Delete NAT Gateway:
  aws ec2 delete-nat-gateway --nat-gateway-id nat-XXXXXXXX

- Release an EIP:
  aws ec2 release-address --allocation-id eipalloc-XXXXXXXX

- Create S3 gateway endpoint (example):
  aws ec2 create-vpc-endpoint --vpc-id vpc-XXXXXXXX --service-name com.amazonaws.us-east-1.s3 --route-table-ids rtb-YYYYYYYY --region us-east-1

- Find unattached EBS volumes:
  aws ec2 describe-volumes --filters Name=status,Values=available --query "Volumes[*].[VolumeId,Size,Tags]" --output table

---

## Notes & best practices
- For production, prioritize AZ-redundant design (NAT per AZ, ALB in multiple AZs).  
- For learning/labs, accept some HA compromises to reduce cost and delete resources after lab.  
- Regularly review Trusted Advisor recommendations and Cost Explorer reports.  
- Never store keys or sensitive credentials in the repo.

---

## Quick checklist (apply now)
- [ ] Release unused EIPs.  
- [ ] Delete unused NAT Gateways / ELBs / volumes / snapshots.  
- [ ] Add VPC endpoint for S3 if you have heavy S3 traffic.  
- [ ] Enable cost allocation tags and create budget alerts.  
- [ ] Consider Savings Plans / Reserved Instances for steady workloads.

Happy building and saving! 💡
