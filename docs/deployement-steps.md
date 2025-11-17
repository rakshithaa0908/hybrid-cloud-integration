# Deployment Steps for Hybrid Cloud Integration

This document provides the full deployment process for building two VPCs in different AWS regions and connecting them through a VPC peering connection. Follow the steps in order to create the environment, configure routing, and verify private communication between EC2 instances.

---

## 1. Create the Environment in N. Virginia (us-east-1)

### Create VPC
- Name: `myvpc1`
- IPv4 CIDR block: `10.0.0.0/16`

### Create Subnet
- Name: `mysubnet1`
- Availability Zone: `us-east-1a`
- Subnet CIDR: `10.0.0.0/24`

### Create Internet Gateway
- Name: `myigw`
- Create and attach to `myvpc1`

### Configure Route Table
- Rename the main route table to `myrt`
- Add route:
  - Destination: `0.0.0.0/0`
  - Target: `myigw`
- Associate `mysubnet1` with `myrt`

### Launch EC2 Instance
- Name: `server1`
- VPC: `myvpc1`
- Subnet: `mysubnet1`
- Auto assign public IP: enabled
- Security group: allow all traffic
- Key pair: `mykey1`

---

## 2. Create the Environment in Mumbai (ap-south-1)

### Create VPC
- Name: `myvpc2`
- IPv4 CIDR block: `172.16.0.0/16`

### Create Subnet
- Name: `mysubnet2`
- Availability Zone: `ap-south-1a`
- Subnet CIDR: `172.16.0.0/24`

### Create Internet Gateway
- Name: `myigw2`
- Create and attach to `myvpc2`

### Configure Route Table
- Rename the main route table to `myrt2`
- Add route:
  - Destination: `0.0.0.0/0`
  - Target: `myigw2`
- Associate `mysubnet2` with `myrt2`

### Launch EC2 Instance
- Name: `server2`
- VPC: `myvpc2`
- Subnet: `mysubnet2`
- Auto assign public IP: enabled
- Security group: allow all traffic
- Key pair: `mykey`

---

## 3. Create VPC Peering Connection

### From N. Virginia
- Open VPC Peering
- Name: `mypeering`
- Select requester VPC: `myvpc1`
- Choose peer region: `ap-south-1`
- Select peer VPC: `myvpc2`
- Create request

### From Mumbai
- Open Peering Connections
- Select the pending request
- Accept the request

---

## 4. Update Route Tables for Peering

### In Mumbai (`myrt2`)
Add route:
- Destination: `10.0.0.0/16`
- Target: `mypeering`

### In N. Virginia (`myrt`)
Add route:
- Destination: `172.16.0.0/16`
- Target: `mypeering`

---

## 5. Verify Connectivity

### SSH into either EC2 server and run:
sudo su -
ping <PRIVATE-IP-OF-OTHER-SERVER>

Example: ping 10.0.0.242

If the setup is correct, the ping will return responses.

---
