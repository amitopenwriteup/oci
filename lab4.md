# Lab: Managing Oracle Cloud Infrastructure Registry (OCIR)

## Objective

In this lab, you will learn the fundamentals of Oracle Cloud Infrastructure Registry (OCIR), now more formally known as **OCI Container Registry (OCIR)**. You will create a repository, push a Docker image to it, pull it back down, and perform basic image management tasks (tagging, listing, and deleting images).

## Prerequisites

- An Oracle Cloud Infrastructure (OCI) account with an active tenancy
- A user account with permissions to manage repositories (an IAM policy granting `manage repos` in the compartment)
- Docker installed on your local machine or a Cloud Shell / Compute instance
- The OCI CLI installed and configured (optional, but useful for automation)
- Your **tenancy namespace** (Object Storage namespace), **region key**, and **username**

## Part 0: Concepts Overview

Before starting, review these core OCIR concepts:

| Concept | Description |
|---|---|
| **Registry** | The OCIR service endpoint for a given region, e.g. `iad.ocir.io`, `phx.ocir.io` |
| **Repository** | A named collection of related container images (e.g. `myapp`) |
| **Image** | A versioned container image identified by a repository name + tag |
| **Tag** | A label attached to an image version (e.g. `1.0`, `latest`) |
| **Auth Token** | A generated token used in place of your password to authenticate `docker login` |
| **Repository visibility** | Public or Private — controls whether pulls require authentication |
| **Compartment** | The OCI compartment that owns and scopes the repository |

Image path format:
```
<region-key>.ocir.io/<tenancy-namespace>/<repository-name>:<tag>
```

Example:
```
iad.ocir.io/mytenancy/myapp:1.0
```

---

## Part 1: Create a Repository

### Step 1.1 — Sign in to the OCI Console
1. Log in to the [OCI Console](https://cloud.oracle.com).
2. Open the navigation menu and go to **Developer Services > Container Registry**.

### Step 1.2 — Create a new repository
1. Select the compartment where you want the repository to live.
2. Click **Create Repository**.
3. Choose repository type:
   - **Private** (recommended for this lab)
4. Enter a repository name, e.g. `devops-lab/myapp` (you can use a parent name as a namespace-like prefix).
5. Click **Create Repository**.

### Step 1.3 — Note the repository details
- Record the **region key** (e.g. `iad`, `phx`, `lhr`) from the console URL or region selector.
- Record your **tenancy namespace**: **Governance & Administration > Tenancy Details > Object Storage Namespace**.

### Step 1.4 — Generate an Auth Token
1. Click your profile icon (top-right) > **User Settings**.
2. Under **Resources**, click **Auth Tokens**.
3. Click **Generate Token**, give it a description (e.g. `ocir-lab-token`), and click **Generate Token**.
4. **Copy the token immediately** — it is shown only once.

---

## Part 2: Push an Image to OCIR

### Step 2.1 — Build or pull a sample image
If you don't already have an image, pull a small public one to use for the lab:
```bash
docker pull nginx:latest
```

### Step 2.2 — Log in to OCIR
```bash
docker login <region-key>.ocir.io
```
When prompted:
- **Username:** `<tenancy-namespace>/<username>` (or `<tenancy-namespace>/oracleidentitycloudservice/<username>` for federated/IDCS users)
- **Password:** the auth token generated in Step 1.4

Example:
```bash
docker login iad.ocir.io
Username: mytenancy/john.doe@example.com
Password: ****************
```

### Step 2.3 — Tag the image for OCIR
```bash
docker tag nginx:latest iad.ocir.io/mytenancy/devops-lab/myapp:1.0
```

### Step 2.4 — Push the image
```bash
docker push iad.ocir.io/mytenancy/devops-lab/myapp:1.0
```

### Step 2.5 — Verify in the Console
1. Navigate to **Developer Services > Container Registry**.
2. Open your repository (`devops-lab/myapp`).
3. Confirm the image with tag `1.0` appears, along with its digest, size, and push timestamp.

---

## Part 3: Pull an Image from OCIR

### Step 3.1 — Remove the local copy (optional, to prove the pull works)
```bash
docker rmi iad.ocir.io/mytenancy/devops-lab/myapp:1.0
```

### Step 3.2 — Pull the image back down
```bash
docker pull iad.ocir.io/mytenancy/devops-lab/myapp:1.0
```

### Step 3.3 — Pull by digest (immutable reference)
1. In the Console, open the image details and copy the **digest** (starts with `sha256:`).
2. Pull using the digest instead of a tag:
```bash
docker pull iad.ocir.io/mytenancy/devops-lab/myapp@sha256:<digest>
```

### Step 3.4 — Test running the pulled image
```bash
docker run -d -p 8080:80 iad.ocir.io/mytenancy/devops-lab/myapp:1.0
curl http://localhost:8080
```

---

## Part 4: Image Management Tasks

### Step 4.1 — Add another tag to an existing image
```bash
docker tag iad.ocir.io/mytenancy/devops-lab/myapp:1.0 \
           iad.ocir.io/mytenancy/devops-lab/myapp:latest
docker push iad.ocir.io/mytenancy/devops-lab/myapp:latest
```

### Step 4.2 — List images and tags (Console)
1. Go to your repository.
2. Review the **Images** tab — note tag, digest, size, and last pushed date for each version.

### Step 4.3 — List images and tags (OCI CLI)
```bash
oci artifacts container image list \
  --compartment-id <compartment-ocid> \
  --repository-name devops-lab/myapp
```

### Step 4.4 — Change repository visibility
1. Open the repository in the Console.
2. Click **Edit Repository**.
3. Toggle between **Private** and **Public**, then save.
4. (Optional) Test an unauthenticated pull if set to Public:
```bash
docker pull iad.ocir.io/mytenancy/devops-lab/myapp:1.0
```

### Step 4.5 — Delete an image tag
1. In the Console, open the repository, select the image version.
2. Click the **⋮** menu next to the tag, choose **Delete**, and confirm.

Or via CLI:
```bash
oci artifacts container image delete \
  --image-id <image-ocid>
```

### Step 4.6 — Set up a retention policy (optional, advanced)
1. In the repository, go to **Retention Policies**.
2. Click **Create Retention Policy**.
3. Define rules, e.g. "keep only the last 5 tagged images" or "delete untagged images older than 30 days."
4. Save and apply.

### Step 4.7 — Delete the repository (cleanup)
1. Ensure all images you want to keep are pulled locally or backed up elsewhere.
2. In the Console, open the repository, click **Delete Repository**.
3. Type the repository name to confirm, then delete.

---

## Lab Summary

In this lab you:
- Learned key OCIR concepts (registry, repository, image, tag, auth token)
- Created a private repository in OCIR
- Authenticated Docker to OCIR using an auth token
- Tagged and pushed a container image
- Pulled the image back by tag and by digest
- Performed image management tasks: tagging, listing, changing visibility, deleting images, and setting retention policies

## Cleanup Checklist

- [ ] Delete test images/tags no longer needed
- [ ] Delete the auth token if it was created solely for this lab
- [ ] Delete the repository if it was created solely for this lab
- [ ] Remove local Docker images with `docker rmi`
