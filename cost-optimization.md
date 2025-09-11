# AWS Cost Optimization Tips – Save Money on VPC Setup

When designing your AWS network, optimizing cost is essential without compromising performance or security.

---

## ✅ Best Practices

✔ **Use one NAT Gateway per region** unless you need redundancy.  
✔ **Use static Elastic IPs** only where required.  
✔ **Delete unused resources** like unused instances, EIPs, and load balancers.  
✔ **Use VPC Endpoints** for S3 or DynamoDB to avoid NAT Gateway charges.  
✔ **Enable billing alerts** and monitor usage.  
✔ **Select appropriate instance types** based on workload.

---

## ✅ Example Cost Saving Actions

1. Use **one NAT Gateway** for both private subnets instead of one per AZ.  
2. Avoid **dedicated tenancy** unless absolutely required → choose default tenancy.  
3. Clean up unused **Elastic IPs** → they incur charges.  
4. Monitor **data transfer** → reduce unnecessary traffic.

---

## ✅ Notes

- High availability is good, but balance it with cost constraints.
- Review your architecture regularly to ensure cost-efficiency.
- AWS Trusted Advisor is helpful for cost analysis.

---

Happy building and saving! 💡
