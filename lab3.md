# Creating a Virtual Machine (VM) Instance in Oracle Cloud Infrastructure (OCI) — Console Guide

This guide walks through creating a Compute VM instance in Oracle Cloud using the **OCI Web Console** only (no CLI required).

---

## Prerequisites

- An active Oracle Cloud account (Free Tier or paid).
- Access to the [OCI Console](https://cloud.oracle.com).
- A compartment where you have permission to create resources.
- An SSH key pair (public/private key) if you plan to connect via SSH, or you can let OCI generate one for you during setup.

---

## Step 1: Sign In to the OCI Console

1. Go to **https://cloud.oracle.com**.
2. Enter your **Cloud Account Name / Tenancy**.
3. Enter your **Username** and **Password**.
4. Click **Sign In**.

---

## Step 2: Open the Compute Instances Page

1. Click the **hamburger menu (☰)** in the top-left corner.
2. Navigate to **Compute** → **Instances**.
3. On the Instances page, select the correct **Compartment** from the left-hand panel (compartment where you want the VM created).

---

## Step 3: Start Instance Creation

1. Click the **Create Instance** button.
2. You'll land on the **Create Compute Instance** page, which has several sections to fill in.

---

## Step 4: Configure Basic Information

1. **Name**: Enter a display name for the instance (e.g., `my-web-server-01`).
   - This is just a label; it doesn't have to be unique.
2. **Create in Compartment**: Confirm/select the compartment.
3. (Optional) Add **Tags** if your organization uses tagging policies.

---

## Step 5: Choose Placement (Availability Domain / Fault Domain)

1. Under **Placement**, review the **Availability Domain (AD)** — OCI usually auto-selects one.
2. You can change the AD if you need instances spread across domains for high availability.
3. Fault Domain is typically left on **Auto-assign**.

---

## Step 6: Select the Image and Shape

1. Under **Image and shape**, click **Edit** (or it may already be expanded).
2. **Image**:
   - Click **Change Image**.
   - Choose an OS (e.g., Oracle Linux, Ubuntu, CentOS, Windows).
   - Select the specific version/build.
   - Click **Select Image**.
3. **Shape**:
   - Click **Change Shape**.
   - Choose the shape series:
     - **Virtual Machine** (standard) or **Bare Metal** (dedicated hardware).
   - Pick a shape family (e.g., `VM.Standard.E4.Flex`, `VM.Standard.A1.Flex` for Free Tier Arm-based instances).
   - If using a **Flex shape**, set the number of **OCPUs** and amount of **Memory (GB)**.
   - Click **Select Shape**.

> **Free Tier Tip:** The Always Free tier typically allows `VM.Standard.E2.1.Micro` (AMD) or up to 4 OCPUs / 24 GB total on `VM.Standard.A1.Flex` (Arm-based Ampere).

---

## Step 7: Configure Networking

1. Under **Primary network**, you have two options:
   - **Select existing virtual cloud network (VCN)** — pick an existing VCN and Subnet.
   - **Create new virtual cloud network** — OCI will auto-generate a VCN, subnet, internet gateway, and route table for you.
2. **Subnet**: Choose Public Subnet (if you need internet access) or Private Subnet (internal only).
3. **Public IPv4 address**:
   - Check **Assign a public IPv4 address** if you want to access the VM over the internet.
4. (Optional) Configure advanced networking options like VNIC name, private IP, or additional VNICs later after creation.

---

## Step 8: Add SSH Keys

1. Under **Add SSH keys**, choose one:
   - **Generate a key pair for me** — download the private key immediately (you won't be able to retrieve it later).
   - **Upload public key files (.pub)** — upload your own existing public key.
   - **Paste public keys** — paste the key text directly.
2. Save the private key securely on your local machine — you'll need it to SSH into the instance.

> **Note:** For Windows images, you'll set a password instead of SSH keys (via a password reset on first boot).

---

## Step 9: Configure Boot Volume (Optional)

1. Expand **Boot volume**.
2. (Optional) Specify a custom **boot volume size** (in GB) if you need more than the default.
3. (Optional) Enable **Use in-transit encryption**.
4. (Optional) Specify a custom **Vault/Key** for encryption if you use OCI Vault.

---

## Step 10: Review and Create

1. Scroll down and review the **Summary** panel on the right side — it shows configuration and estimated cost.
2. Click **Create** at the bottom of the page.
3. OCI will provision the instance. You'll see the instance state change:
   - **Provisioning** → **Running**

---

## Step 11: Access Your Instance

1. Once state shows **Running**, click on the instance name to open its details page.
2. Note the **Public IP Address** shown under Instance Access.
3. Connect via SSH (Linux/macOS/WSL terminal):
   ```bash
   ssh -i /path/to/private_key opc@<PUBLIC_IP>
   ```
   - Default username is usually `opc` (Oracle Linux) or `ubuntu` (Ubuntu images).
4. For Windows instances, use **Remote Desktop (RDP)** with the public IP and generated password.

---

## Step 12 (Optional): Configure Security List / Network Security Group for Access

If you can't connect, you may need to open ports:

1. Go to **Networking** → **Virtual Cloud Networks**.
2. Select your VCN → select the **Subnet** used by the instance.
3. Click on the **Security List** (or **Network Security Group** if used).
4. Click **Add Ingress Rules**.
5. Add a rule, e.g.:
   - Source CIDR: `0.0.0.0/0` (or your IP for tighter security)
   - IP Protocol: TCP
   - Destination Port Range: `22` (SSH), `80` (HTTP), `443` (HTTPS), etc.
6. Click **Add Ingress Rules** to save.

---

## Step 13: Managing the Instance

From the instance details page you can:
- **Stop / Start / Reboot** the instance.
- **Terminate** the instance (deletes it — be careful, this is often irreversible for the boot volume unless you choose to preserve it).
- **Edit** shape (resize OCPUs/memory for Flex shapes).
- Attach additional **block volumes** for extra storage.
- View **Metrics** (CPU, network, memory) under the Monitoring tab.

---

## Quick Checklist

- [ ] Signed in to OCI Console
- [ ] Selected correct compartment
- [ ] Named the instance
- [ ] Selected image (OS)
- [ ] Selected shape (and configured OCPU/memory if Flex)
- [ ] Configured VCN/subnet and public IP
- [ ] Added SSH key (or noted Windows password)
- [ ] Reviewed boot volume settings
- [ ] Clicked Create
- [ ] Verified instance is Running
- [ ] Connected via SSH/RDP
- [ ] Opened necessary firewall/security list ports

---

*Guide based on the standard OCI Console workflow (Compute → Instances → Create Instance). Console UI labels may vary slightly across OCI updates.*
