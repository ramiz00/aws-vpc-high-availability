## ✅ **5. vpc-peering.md**

Save as `vpc-peering.md`:

```markdown
# VPC Peering – Connect Multiple VPCs Securely

This guide shows how to connect two VPCs in different regions using VPC Peering.

---

## ✅ Step 1 – Create a Second VPC

1. Go to **VPC → Create VPC** in region `us-west-2`.
2. Name it `vprof-db`.
3. CIDR block: `172.22.0.0/16`.

---

## ✅ Step 2 – Create Route Table

1. Name it `vprof-db-rt`.
2. Associate it with the new subnets.

---

## ✅ Step 3 – Create Peering Connection

1. Go to **VPC Peering → Create Peering Connection**.
2. Name: `vprof-nc-peering`.
3. Requester → `vprofile-vpc`.
4. Accepter → `vprof-db` in `us-west-2`.
5. Send request → Accept it from the second account.

---

## ✅ Step 4 – Update Route Tables

### In `vprofile-vpc`:
- Edit route → Destination: `172.22.0.0/16`.
- Target → Select Peering Connection.

### In `vprof-db`:
- Edit route → Destination: `172.20.0.0/16`.
- Target → Select Peering Connection.

---

## ✅ Step 5 – Update Security Groups

- Allow traffic from the other VPC CIDR block in inbound rules.
- Use specific ports (like SSH or HTTP) as needed.

---

### ✅ Notes

- VPC Peering helps in building multi-region architecture.
- Update routes on both sides to ensure connectivity.
- Secure communication by controlling access in security groups.

---

![VPC Peering](images/vpc-peering.png)
