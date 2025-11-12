# ☁️Hands-on Practice: Amazon S3 Upload & Access an Image Using ACL (Enable)

## 📘Overview
This hands-on lab shows how to create S3 buckets, upload an image object, and verify browser access using different ACL-related settings. You will perform two flows:

1. Bucket configured so objects can be made public using object ACL (public-read) verify image loads in browser.  
2. Bucket configured to block public access (private) upload object as private and verify browser shows *Access Denied*.

---

## ✅Prerequisites
- AWS account with permission to create S3 buckets and objects.
- Access to the AWS Management Console (preferred for this lab).
- An Object to upload (e.g., `example.jpg`).

---

## 🧠Key Concepts (short)
- **S3 Bucket**: Container for objects. Bucket names are globally unique.
- **Object**: The stored item. An object has three components:
  1. **Data** — the file content (image, video, document, etc.).
  2. **Metadata** — information about the object (size, content-type, encryption flag, timestamps).
  3. **Key** — the object's name/path inside the bucket (unique within a bucket).
- **ACL (Access Control List)**: legacy, object-level and bucket-level grants. Can give `public-read` to an object.
- **Block Public Access**: account/bucket-level setting that prevents public access even if an object ACL tries to grant it.

---

## ⚠️Safety Notes / Warnings
- Public-read objects are accessible globally. Never use public-read for sensitive data.
- Disabling **Block Public Access** reduces security only do it for controlled test objects.
- AWS is constantly evolving; console labels may change. Look for equivalent settings if UI differs.

---

## 🪣 Flow 1 - Create Bucket (allow object-level ACLs) & Upload a Public Image
### Goal - Let's create a bucket with object ACLs permitted, upload an image and mark the object `public-read`, then open the Object URL in a browser to verify it loads.

### 🖥️Console Steps

1. **Open** the AWS Console → Services → **S3**.
2. Click **Create bucket**.
3. Choose **Bucket name** (must be globally unique) and **Region**.
4. Under **Object ownership**:
   - If the console shows **Object ownership** options, select the option that allows ACLs (for older or alternate UIs this means do not enable "ACLs disabled" or "Bucket owner enforced"). The goal: allow object ACL changes.
5. In **Block public access settings for this bucket**:
   - **Uncheck** the option **Block all public access** (or toggle off the block that prevents public ACLs).
   - When prompted, check **I acknowledge that the current settings might result in this bucket and the objects within becoming public.**
6. Click **Create bucket**.

> Result: A bucket that allows object ACLs and can host public objects.

### 📤Upload an object and set `public-read`
1. Open the bucket, click **Upload** → **Add files**, choose the image (e.g., `example.jpg`).
2. Click **Next** until you reach **Permissions**.
3. Under **Predefined ACLs**, select **Grant public-read access** (or the UI option labeled similarly).
4. Click **Upload** and wait for the upload to finish.

### 🌐Verify via browser
1. In the bucket list, click the uploaded object.
2. In the **Properties** or details pane, copy the **Object URL** (example):
   ```
   https://<bucket-name>.s3.<region>.amazonaws.com/example.jpg
   ```
3. Paste the URL in a browser. Expected result: the image appears (object is public).

---

## Flow 2 — 🛡️Create Bucket (block public access) and Upload a Private Image

### Goal - Let's create a bucket that blocks public access, upload an image as `Private`, then verify browser shows **Access Denied**.

### 🖥️Console Steps

1. **Create new bucket** (a new one bucket names must be unique).
2. Under **Object ownership**:
   - Keep the default or set **Bucket owner enforced** / disable ACLs if offered (the intent is to ensure the bucket blocks public ACLs).
3. In **Block public access settings for this bucket**:
   - **Ensure** **Block all public access** is **checked** (or do not uncheck any block options).
   - Click **Create bucket**.

> Result: Bucket blocks public access, so object ACLs (including public-read) will have no effect.

### 🔒Upload object as Private
1. Open the bucket, click **Upload** → **Add files**, choose an image (e.g., `example.jpg`).
2. In **Permissions**, choose **Private (recommended)** (or leave default).
3. Click **Upload** and wait for completion.

### 🚫Verify via browser
1. Copy the **Object URL** for the uploaded object.
2. Open the URL in a browser. Expected result: **Access Denied** (object not publicly accessible).
3. To view the object as owner, open it from the AWS Console and click **Open** (object preview) console access is allowed even if URL access is blocked.

---

## 🔁Making an Entire Bucket Public or Private (toggle)

### 🔐Make bucket fully private (block public access)
1. Open the bucket → **Properties** tab.
2. Scroll to **Block public access (bucket settings)**.
3. Check **Block all public access** (or enable all block toggles).
4. Save changes and confirm.

> After this, the bucket and all objects will be non-public from the internet. Previously public object URLs will return **Access Denied**.

### 🌍Make bucket public (allow object ACLs / remove block)
1. Open the bucket → **Properties** tab.
2. Under **Block public access**, uncheck the block settings that prevent public access and acknowledge the warning.
3. Save changes.

> After removing the block, you can set objects to `public-read` again.

---
### 🗺️Overall AWS Infrastructure Layout (EC2, RDS, S3, VPC) (Reference Diagram)
<img width="935" height="443" alt="Cloud S3 architecture" src="https://github.com/user-attachments/assets/428bdf6f-cebb-492d-b3ab-4a4db4e2cc7e" />

---

## ✅Verification checklist (quick)
- [ ] Bucket A created with public ACLs allowed.
- [ ] Uploaded object to Bucket A with **public-read** ACL → object URL shows image in browser.
- [ ] Bucket B created with **Block all public access** enabled.
- [ ] Uploaded object to Bucket B as **Private** → object URL shows **Access Denied** in browser.
- [ ] Toggled Bucket A/B block settings and re-tested to confirm changes take effect.

---

## 🧩Troubleshooting
- **Object still shows Access Denied after setting public-read**:
  - Confirm the bucket-level **Block public access** is **disabled** for that bucket.
  - Check that the object ACL is actually `public-read` using `get-object-acl`.
  - If using AWS Organizations, an account-level block may still prevent public access.
- **Object URL not found (404)**:
  - Confirm the object key and bucket name are correct.
  - Ensure you uploaded to the same region referenced in the URL.
- **Bucket name collisions**:
  - Bucket names must be unique globally. If the name is taken, choose a different name.
- **Console labels differ**:
  - AWS sometimes updates UI text. Look for equivalent options: *Block public access*, *Object ownership*, *Predefined ACLs*, *Grant public-read*.

---

## 🧹Cleanup (recommended)
1. Delete uploaded objects.
2. Delete the buckets you created.
3. Review billing (S3 storage is cheap for small files but tidy up to avoid surprises).

---

## 📚References / Further reading
- S3 Object Ownership and ACLs refer to AWS docs in console help if you need deeper explanation.
- Use S3 Bucket Policies for fine-grained, auditable public/conditional access rather than relying on ACLs for production.

---

## ✍️Author
**Rushikesh Dase**  
Cloud Computing Enthusiast | AWS Learner  
📧 daserushikesh@gmail.com  
🔗 https://www.linkedin.com/in/rushi-dase  
**Category:** | AWS | Cloud Computing | EC2 | VPC | Load Balancing | RDS | S3 |
