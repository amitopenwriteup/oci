# OCI Workshop Lab: Building a Complete VCN Stack

## Overview

In this lab, you will build a fully functional Virtual Cloud Network (VCN) stack on Oracle Cloud Infrastructure (OCI), including public and private subnets, an Internet Gateway, a NAT Gateway, route tables, and security lists. By the end, you'll have a network architecture ready to host a public-facing web server and a private backend resource.

**Estimated time:** 45–60 minutes

---

## Prerequisites

- An active OCI account with access to a compartment
- Appropriate IAM permissions to create networking resources
- Basic familiarity with the OCI Console

---

## Step 1: Create the VCN

1. Open the OCI Console navigation menu → **Networking** → **Virtual Cloud Networks**.
2. Select your **Compartment**.
3. Click **Start VCN Wizard** *or* **Create VCN Only** (this lab uses the manual "Create VCN Only" approach for full control).
4. Fill in the form:
   | Field | Value |
   |---|---|
   | Name | `workshop-vcn` |
   | Create In Compartment | *(your compartment)* |
   | IPv4 CIDR Blocks | `10.0.0.0/16` |
   | IPv6 Prefixes | Leave disabled |
5. Click **Create VCN**.

✅ **Checkpoint:** Your VCN should now appear in the VCN list with state **Available**.

---

## Step 2: Create the Internet Gateway

1. From your VCN's detail page, go to **Internet Gateways** (left panel).
2. Click **Create Internet Gateway**.
3. Name it `workshop-igw`.
4. Click **Create Internet Gateway**.

---

## Step 3: Create the NAT Gateway

1. From the VCN detail page, go to **NAT Gateways**.
2. Click **Create NAT Gateway**.
3. Name it `workshop-nat-gw`.
4. Leave the public IP as auto-assigned.
5. Click **Create NAT Gateway**.

---

## Step 4: Create Route Tables

### 4a. Public Route Table

1. Go to **Route Tables** → **Create Route Table**.
2. Name: `public-rt`.
3. Add a route rule:
   | Target Type | Destination CIDR | Target |
   |---|---|---|
   | Internet Gateway | `0.0.0.0/0` | `workshop-igw` |
4. Click **Create**.

### 4b. Private Route Table

1. Click **Create Route Table** again.
2. Name: `private-rt`.
3. Add a route rule:
   | Target Type | Destination CIDR | Target |
   |---|---|---|
   | NAT Gateway | `0.0.0.0/0` | `workshop-nat-gw` |
4. Click **Create**.

---

## Step 5: Create Security Lists

### 5a. Public Security List

1. Go to **Security Lists** → **Create Security List**.
2. Name: `public-seclist`.
3. **Ingress Rules:**
   | Source CIDR | Protocol | Destination Port |
   |---|---|---|
   | `0.0.0.0/0` | TCP | 22 (SSH) |
   | `0.0.0.0/0` | TCP | 80 (HTTP) |
   | `0.0.0.0/0` | TCP | 443 (HTTPS) |
4. **Egress Rules:** Allow all traffic (`0.0.0.0/0`, All Protocols) — default.
5. Click **Create Security List**.

### 5b. Private Security List

1. Click **Create Security List** again.
2. Name: `private-seclist`.
3. **Ingress Rules:**
   | Source CIDR | Protocol | Destination Port |
   |---|---|---|
   | `10.0.0.0/24` | TCP | 22 (SSH from public subnet only) |
   | `10.0.0.0/24` | TCP | 3306 (example: DB access from web tier) |
4. **Egress Rules:** Allow all traffic — default.
5. Click **Create Security List**.

---

## Step 6: Create the Subnets

### 6a. Public Subnet

1. Go to **Subnets** → **Create Subnet**.
2. Fill in:
   | Field | Value |
   |---|---|
   | Name | `public-subnet` |
   | Subnet Type | Regional |
   | CIDR Block | `10.0.0.0/24` |
   | Route Table | `public-rt` |
   | Subnet Access | **Public Subnet** |
   | Security List | `public-seclist` |
   | DNS Resolution | Enabled |
3. Click **Create Subnet**.

### 6b. Private Subnet

1. Click **Create Subnet** again.
2. Fill in:
   | Field | Value |
   |---|---|
   | Name | `private-subnet` |
   | Subnet Type | Regional |
   | CIDR Block | `10.0.1.0/24` |
   | Route Table | `private-rt` |
   | Subnet Access | **Private Subnet** |
   | Security List | `private-seclist` |
   | DNS Resolution | Enabled |
3. Click **Create Subnet**.

---

## Step 7: Validate the Stack

Confirm the following in your VCN detail page:

- [ ] VCN `workshop-vcn` shows CIDR `10.0.0.0/16`
- [ ] Internet Gateway `workshop-igw` is **Available**
- [ ] NAT Gateway `workshop-nat-gw` is **Available**
- [ ] `public-rt` routes `0.0.0.0/0` → Internet Gateway
- [ ] `private-rt` routes `0.0.0.0/0` → NAT Gateway
- [ ] `public-subnet` (10.0.0.0/24) uses `public-rt` and `public-seclist`
- [ ] `private-subnet` (10.0.1.0/24) uses `private-rt` and `private-seclist`

---

## Step 8 (Optional): Launch a Test Instance

1. Navigate to **Compute** → **Instances** → **Create Instance**.
2. Choose an image (e.g., Oracle Linux) and shape (VM.Standard.E4.Flex or Always Free eligible shape).
3. Under **Networking**, select `workshop-vcn` and `public-subnet`.
4. Assign a public IP.
5. Add your SSH key.
6. Click **Create**.
7. Once running, test SSH access:
   ```bash
   ssh -i /path/to/private_key opc@<PUBLIC_IP>
   ```

---

## Cleanup (Important for Free Tier / Cost Control)

To avoid unwanted charges, delete resources in this order once done:

1. Terminate any Compute instances.
2. Delete both Subnets.
3. Delete both Security Lists.
4. Delete both Route Tables (except the default one, if unused it's fine to leave).
5. Delete the NAT Gateway.
6. Delete the Internet Gateway.
7. Delete the VCN.

---

## Summary

You built a two-tier VCN architecture with:
- A public subnet for internet-facing resources (via Internet Gateway)
- A private subnet for internal resources (via NAT Gateway for outbound-only access)
- Dedicated route tables and security lists for each tier

This pattern is the foundation for most production and workshop OCI architectures (web servers, load balancers, databases, Kubernetes clusters, etc.).
