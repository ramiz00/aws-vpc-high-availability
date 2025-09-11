# Bastion Host Setup – Secure Access to Private Subnets

A Bastion Host allows you to securely connect to EC2 instances in a private subnet without exposing them to the internet.

---

## ✅ Step 1 – Create Security Group

1. Go to **EC2 → Security Groups → Create Security Group**.
2. Name it `vprof-bastion-sg`.
3. Select the `vprofile-vpc`.
4. Add inbound rule:
   - Type: SSH
   - Protocol: TCP
   - Port Range: 22
   - Source: Your Corporate/Public IP (for security)

---

## ✅ Step 2 – Create Key Pair

1. Go to **EC2 → Key Pairs → Create key pair**.
2. Name it `vprof-bastion-kp`.
3. Download the `.pem` file → keep it safe.

---

## ✅ Step 3 – Launch Bastion EC2 Instance

1. Go to **EC2 → Launch Instance**.
2. Choose Ubuntu AMI (free tier available).
3. Use `vprof-bastion-kp` key pair.
4. Select `vprof-bastion-sg` as the security group.
5. Place it in `public-sub-1a`.

---

## ✅ Step 4 – Connect to Bastion Host

```bash
ssh -i vprof-bastion-kp.pem ubuntu@<Public_IP_of_Bastion>

---

## ✅ Step 5 – Connect to Private Instance

- Create another key pair → web-key.pem.
```bash
scp -i Vprof-Basiton-KP.pem web-key.pem ubuntu@<Public_IP_of_Bastion>:/home/ubuntu 

**From Bastion Host, connect using:**

ssh -i web-key.pem ec2-user@<Private_IP_of_Private_Instance>

✅ Notes

Bastion Host acts as a jump server.

Restrict SSH access using security groups.

Always use key pairs for secure authentication.