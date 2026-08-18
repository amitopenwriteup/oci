# Oracle Cloud Storage Workshop Lab
## Object Storage and Block Storage Hands-On Testing

---

## Table of Contents
1. [Lab Overview](#lab-overview)
2. [Prerequisites](#prerequisites)
3. [Object Storage Labs](#object-storage-labs)
4. [Block Storage Labs](#block-storage-labs)

---

## Lab Overview

This workshop provides hands-on experience with Oracle Cloud Infrastructure (OCI) storage services:
- Object Storage: Scalable, API-based storage for unstructured data
- Block Storage: High-performance network-attached block volumes

### Lab Duration
- Estimated Time: 2-3 hours
- Difficulty Level: Intermediate
- Cost: Minimal (use free tier resources when possible)

### Learning Objectives
By the end of this workshop, you will be able to:
- Create and manage Object Storage buckets
- Upload, download, and manage objects
- Implement lifecycle policies and versioning
- Create and manage Block Storage volumes
- Attach volumes to compute instances
- Create and restore from snapshots

---

## Prerequisites

### Required Access
- Oracle Cloud Infrastructure (OCI) account with administrator privileges
- Active compute instance in your VCN (for Block Storage labs)
- SSH access to the compute instance

### Required Tools
- Web browser for OCI Console access
- SSH client (for accessing compute instance)

### Access OCI Console
1. Open your web browser
2. Go to https://console.oracle.com
3. Enter your cloud account name and click Next
4. Enter your username and password
5. You should see the OCI Dashboard

---

## Object Storage Labs

### Lab 1: Creating and Managing Buckets

#### Objective
Learn to create and configure Object Storage buckets with different storage tiers.

#### Lab Steps

##### Step 1.1: Create a Standard Storage Bucket

1. In OCI Console, click the hamburger menu (three horizontal lines)
2. Navigate to Storage > Buckets
3. Ensure you are in the correct compartment (check dropdown at top left)
4. Click "Create Bucket" button (blue button on top right)
5. In the Create Bucket dialog:
   - Name: Enter "workshop-bucket-standard"
   - Storage Tier: Select "Standard"
   - Click "Create" button

6. Wait for the bucket to be created (should show "Active" state)
7. Click on the bucket name to view its details

Verify the bucket was created:
- Bucket name appears in the list
- State shows as "Active"
- Storage tier shows "Standard"



##### Step 1.3: Enable Versioning on Standard Bucket

1. Click on "workshop-bucket-standard" to open it
2. On the bucket details page, look for "Edit" or "Actions" menu
3. Select "Edit" option
4. Find "Versioning" setting and toggle it to "Enabled"
5. Click "Save" button

Expected: Versioning status should now show as "Enabled"

##### Step 1.4: Configure Lifecycle Management

1. Click on "workshop-bucket-standard" bucket
2. In the left menu panel, look for "Lifecycle Policies" option
3. Click "Create Lifecycle Policy"
4. Set up the policy:
   - Rule Name: Enter "Archive old objects"
   - Rule Action: Select "Archive"
   - Time After Creation: Enter 30 days
   - Status: Enabled
5. Click "Create" button

Expected: Lifecycle policy appears in the policy list

---

### Lab 2: Uploading and Managing Objects

#### Objective
Practice uploading, downloading, and managing objects in buckets.

#### Lab Steps

##### Step 2.1: Create Test Data Files

Before uploading, create test files on your local computer:

On Linux/Mac:
```bash
# Create small test file
touch test_small.txt
echo "This is a small test file" > test_small.txt

# Create medium test file
dd if=/dev/zero of=test_medium.bin bs=1M count=10

# Create document
echo "Workshop Test Document" > test_doc.txt
```

##### Step 2.2: Upload Objects to Standard Bucket

1. Click on "workshop-bucket-standard" to open it
2. Click the blue "Upload" button
3. In the upload dialog:
   - Click "select files" or drag files into the area
   - Select "test_small.txt" from your computer
   - Click "Upload" button

4. Wait for upload to complete
5. Verify the file appears in the bucket contents

To upload with folder structure:
1. Click "Upload" again
2. Select "test_doc.txt"
3. In the Object Name field, change it to "documents/test_doc.txt"
4. Click "Upload"

Expected Output:
- Files appear in the bucket listing
- Each file shows its size and creation date
- Files are accessible from the bucket

##### Step 2.3: View Object Details

1. Click on any uploaded object (file name)
2. View the object details panel showing:
   - Object name
   - Size in bytes
   - Storage tier
   - Creation date
   - Metadata information

3. To download the object:
   - Click the three dots menu (more options)
   - Select "Download"
   - File will download to your computer

##### Step 2.4: Test Versioning

1. Click on "workshop-bucket-standard" bucket
2. Click "Upload" button
3. Select "test_doc.txt" file again
4. Keep the same object name: "documents/test_doc.txt"
5. Click "Upload" (this creates a new version)

To view versions:
1. Click on "documents/test_doc.txt" object
2. Look for "Versions" tab or section in the details panel
3. You should see multiple versions listed with different timestamps

##### Step 2.5: Delete Objects

1. Click on "workshop-bucket-standard" bucket
2. Find the object you want to delete (e.g., "test_medium.bin")
3. Click the three dots menu next to the object
4. Select "Delete"
5. Confirm the deletion by clicking "Delete" in the confirmation dialog

Expected: Object no longer appears in the bucket listing

---

### Lab 3: Access Control and Permissions

#### Objective
Implement and test access control for Object Storage.

#### Lab Steps

##### Step 3.1: Create Pre-Authenticated Requests (PAR)

Pre-Authenticated Requests allow access to objects without OCI credentials.

1. Click on "workshop-bucket-standard" bucket
2. Click on any object (e.g., "documents/test_doc.txt")
3. Look for "Create Pre-Authenticated Request" option
   - Click the three dots menu or look in the Actions menu
4. In the Create PAR dialog:
   - Name: Enter "par-read-access"
   - Access Type: Select "Permit reads on this object"
   - Expiration: Select a date (e.g., tomorrow)
   - Click "Create Pre-Authenticated Request"

5. Copy the full PAR URL that is displayed

##### Step 3.2: Test PAR Access

The PAR URL allows anyone with the link to access the object without authentication.

To test:
1. Open a new browser window (incognito/private mode recommended)
2. Paste the PAR URL in the address bar
3. Press Enter
4. You should see the file content or download dialog without logging in

To share:
- Send only the PAR URL to others
- They can access the object without OCI credentials
- Access expires on the date you set

##### Step 3.3: View Access Logs

1. Click on "workshop-bucket-standard" bucket
2. In the left menu, look for "Access Logs" or "Audit Logs"
3. View the logs showing who accessed objects and when
4. Logs show object access history and can help with security monitoring

---

### Lab 4: Performance and Monitoring

#### Objective
Monitor and test Object Storage performance.

#### Lab Steps

##### Step 4.1: View Bucket Metrics

1. Click on "workshop-bucket-standard" bucket
2. Look for "Metrics" tab or section
3. You should see metrics including:
   - Total storage used
   - Number of objects
   - Request counts
   - Data transfer amounts

4. To view detailed metrics:
   - Go to hamburger menu > Monitoring > Metrics Explorer
   - Select Namespace: "Object Storage"
   - Select your compartment
   - Choose metrics like:
     - Object Storage Requests
     - Object Storage Data Transfer

##### Step 4.2: Monitor Upload Activity

1. Upload a file to "workshop-bucket-standard"
2. Go to Monitoring > Metrics Explorer
3. Watch the request metrics update
4. This shows real-time storage activity

##### Step 4.3: Check Storage Usage

1. Click on "workshop-bucket-standard" bucket
2. Look at the bucket summary information showing:
   - Total size of all objects
   - Number of objects
   - Storage tier breakdown

---

## Block Storage Labs

### Lab 5: Creating and Attaching Block Volumes

#### Objective
Create and attach Block Storage volumes to compute instances.

#### Prerequisites
- You have a running compute instance
- You know the instance name or OCID
- The instance is in a VCN with available network capacity

#### Lab Steps

##### Step 5.1: Create a Block Volume

1. In OCI Console, click hamburger menu
2. Navigate to Storage > Block Volumes
3. Click "Create Block Volume" button (top right)
4. In the Create Block Volume dialog:
   - Name: Enter "workshop-volume-01"
   - Availability Domain: Select the AD where your instance is located
   - Size (GB): Enter "100"
   - Performance (VPU/GB): Select "Balanced" (10 VPU/GB is good for most workloads)
   - Click "Create Block Volume" button

5. Wait for the volume to reach "Available" state (usually 1-2 minutes)
6. Note the volume OCID for later use

##### Step 5.2: Attach Volume to Your Instance

1. Navigate to Compute > Instances
2. Click on your instance name to view its details
3. Scroll down to "Attached Block Volumes" section
4. Click "Attach Block Volume" button
5. In the attachment dialog:
   - Volume: Select "workshop-volume-01"
   - Attachment Type: Select "iSCSI"
   - Click "Attach" button

6. Wait for attachment to complete (should show "Attached" state)
7. The console will display iSCSI connection information

##### Step 5.3: Connect to Your Instance and Mount the Volume

Now you need to SSH into your instance and mount the volume:

```bash
# SSH into your instance
ssh -i <your_private_key> opc@<instance_public_ip>

# Check what disk was attached
lsblk
# You should see a new disk (usually /dev/sdb) with 100GB size

# Create partition on the new disk
sudo parted /dev/sdb mklabel gpt
sudo parted /dev/sdb mkpart primary ext4 0% 100%

# Format the partition with ext4 filesystem
sudo mkfs.ext4 /dev/sdb1

# Create mount point directory
sudo mkdir -p /mnt/workshop-storage

# Mount the volume
sudo mount /dev/sdb1 /mnt/workshop-storage

# Verify the mount
df -h /mnt/workshop-storage
# Should show 100GB mounted at /mnt/workshop-storage

# Make mount persistent across reboots
echo "/dev/sdb1 /mnt/workshop-storage ext4 defaults 0 0" | sudo tee -a /etc/fstab

# Verify persistence
sudo mount -a
```

---

### Lab 6: Snapshots and Backups

#### Objective
Create and restore snapshots for disaster recovery and backup.

#### Lab Steps

##### Step 6.1: Write Test Data to Volume

First, create some data on the volume:

```bash
# SSH to your instance
ssh -i <your_private_key> opc@<instance_public_ip>

# Create test data on the mounted volume
echo "Important Production Data" | sudo tee /mnt/workshop-storage/test_data.txt
echo "Backup timestamp: $(date)" | sudo tee -a /mnt/workshop-storage/test_data.txt

# Verify the data
sudo cat /mnt/workshop-storage/test_data.txt
```

##### Step 6.2: Create a Snapshot

1. In OCI Console, navigate to Storage > Block Volumes
2. Click on "workshop-volume-01" volume
3. Scroll down to find "Snapshots" section or click "Snapshots" tab
4. Click "Create Snapshot" button
5. In the create snapshot dialog:
   - Name: Enter "workshop-snapshot-01"
   - Description: Enter "Snapshot of volume with test data"
   - Click "Create Snapshot" button

6. Wait for snapshot to reach "Available" state (this may take 5-10 minutes)
7. You can see the snapshot status on the Snapshots page

##### Step 6.3: Create a New Volume from Snapshot

1. Navigate to Storage > Block Volumes
2. Find the "workshop-snapshot-01" snapshot
3. Click the three dots menu on the snapshot
4. Select "Create Volume from Snapshot"
5. In the dialog:
   - Name: Enter "workshop-volume-from-snapshot"
   - Availability Domain: Select same AD as original
   - Click "Create Volume"

6. Wait for new volume to become "Available"

---

## Key Learnings Summary

### Object Storage Best Practices
- Use appropriate storage tier based on access frequency
- Implement lifecycle policies to optimize costs automatically
- Enable versioning for critical data protection
- Use Pre-Authenticated Requests for secure sharing without credentials
- Monitor bucket usage and implement quotas
- Use descriptive names and tags for better organization

### Block Storage Best Practices
- Choose appropriate VPU/GB ratio for performance needs
- Create snapshots before making major changes
- Use snapshots for backup and disaster recovery
- Monitor I/O performance metrics regularly
- Test recovery procedures regularly

### When to Use Each Service

Object Storage is best for:
- Unstructured data (documents, images, videos)
- Backups and archives
- Data lakes and analytics
- Cost-sensitive use cases
- Unlimited scalability needs

Block Storage is best for:
- Database storage
- Virtual machine file systems
- High-performance workloads
- Real-time analytics
- Consistent low-latency requirements

---

## Troubleshooting Guide

### Common Issues and Solutions

#### Issue: Cannot Mount Block Volume
Solution:
1. Verify volume is attached to instance
2. Check the device name with: lsblk
3. Ensure partition exists: sudo fdisk -l /dev/sdb
4. Create partition if missing: sudo parted /dev/sdb mklabel gpt

#### Issue: Slow Object Storage Upload
Solution:
1. Check your network connection speed
2. Verify bucket is in same region as your computer
3. Try uploading smaller files first
4. Use compression before uploading

#### Issue: Cannot Access Objects with PAR
Solution:
1. Verify PAR has not expired
2. Check the URL is complete and correct
3. Try creating a new PAR with extended expiration
4. Check browser cookies and cache

---

## Additional Resources

### OCI Documentation
- Object Storage Documentation
  https://docs.oracle.com/en-us/iaas/Content/Object/home.htm

- Block Storage Documentation
  https://docs.oracle.com/en-us/iaas/Content/Block/Concepts/overview.htm

### Pricing Information
- OCI Pricing Calculator
  https://www.oracle.com/cloud/price-list/

---

## Workshop Completion Checklist

- Lab 1: Created and configured buckets with different tiers
- Lab 2: Uploaded and managed objects successfully
- Lab 3: Tested access control with Pre-Authenticated Requests
- Lab 4: Monitored Object Storage performance
- Lab 5: Created and attached Block volumes
- Lab 6: Created snapshots and created volume from snapshot

Congratulations on completing the Oracle Cloud Storage Workshop!

---

Last Updated: January 2024
Workshop Duration: 2-3 Hours
Difficulty: Intermediate
