# VenueHub Pro - AWS Deployment Guide

**Complete deployment guide for https://www.venuehubpro.in**

Based on actual implementation with Route 53, CloudFront, and S3.

---

## Overview

Deploy VenueHub Pro marketing site with:
- ✅ S3 static website hosting
- ✅ CloudFront CDN with HTTPS
- ✅ Route 53 DNS management
- ✅ Free SSL certificate (AWS ACM)
- ✅ Custom domain (venuehubpro.in)

**Time:** ~1 hour | **Cost:** ~₹105/month

---

## Prerequisites

- AWS Account
- Domain purchased (e.g., venuehubpro.in from GoDaddy)
- `index.html` file ready

---

## Step 1: Create S3 Bucket

### Create & Configure Bucket

1. **AWS Console** → **S3** → **"Create bucket"**
2. **Bucket name:** `hall-manager-pro-marketing`
3. **Region:** `ap-south-1` (Mumbai)
4. **Uncheck** "Block all public access" ✅
5. Click **"Create bucket"**

### Upload Files

1. Click bucket → **"Upload"** → Add `index.html`
2. Click **"Upload"**

### Enable Static Website Hosting

1. Bucket → **Properties** → **"Static website hosting"**
2. Click **"Edit"** → Select **"Enable"**
3. **Index document:** `index.html`
4. **Save changes**
5. **Copy endpoint:** `http://bucket-name.s3-website.ap-south-1.amazonaws.com`

### Set Bucket Policy

1. **Permissions** → **"Bucket policy"** → **"Edit"**
2. Paste:

```json
{
    "Version": "2012-10-17",
    "Statement": [{
        "Sid": "PublicReadGetObject",
        "Effect": "Allow",
        "Principal": "*",
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::hall-manager-pro-marketing/*"
    }]
}
```

3. **Save**

### Test

Open: `http://hall-manager-pro-marketing.s3-website.ap-south-1.amazonaws.com`

---

## Step 2: Set Up Route 53 DNS

**Why Route 53?** Faster DNS validation than GoDaddy (5-10 min vs 2+ days)

### Create Hosted Zone

1. **Route 53** → **"Create hosted zone"**
2. **Domain:** `venuehubpro.in`
3. **Type:** Public
4. **Create**
5. **Copy 4 nameservers** (e.g., `ns-869.awsdns-44.net`)

### Update GoDaddy Nameservers

1. **GoDaddy** → Domain → **"Nameservers"** → **"Change"**
2. Select **"Custom nameservers"**
3. **Paste 4 Route 53 nameservers** (remove trailing dots!)
4. **Save**
5. **Wait 5-10 minutes**

---

## Step 3: Request SSL Certificate

### Create Certificate

1. **Certificate Manager** → **Switch to us-east-1 region** (required!)
2. **"Request certificate"** → **"Public certificate"**
3. **Domains:**
   - `venuehubpro.in`
   - `www.venuehubpro.in`
4. **Validation:** DNS validation
5. **Request**

### Validate via Route 53

1. Click certificate → **"Create records in Route 53"**
2. **Create records** (automatic!)
3. **Wait 5-15 minutes** → Status: "Issued" ✅

---

## Step 4: Create CloudFront Distribution

### Create Distribution

1. **CloudFront** → **"Create distribution"**

### Origin Settings (CRITICAL!)

**Type manually, don't select from dropdown:**

- **Origin domain:** `hall-manager-pro-marketing.s3-website.ap-south-1.amazonaws.com`
- **Protocol:** HTTP only

### Cache Behavior

- **Viewer protocol:** Redirect HTTP to HTTPS
- **Methods:** GET, HEAD
- **Cache policy:** CachingOptimized

### Settings

- **Alternate domains:** `www.venuehubpro.in`
- **SSL certificate:** Select `venuehubpro.in`
- **Default root object:** Leave empty

### Deploy

1. **Create distribution**
2. **Wait 10-15 minutes**
3. **Copy domain:** `d1o5b6xnnn6e9g.cloudfront.net`

---

## Step 5: Create DNS Record

### Add WWW Record

1. **Route 53** → Hosted zone → **"Create record"**
2. **Name:** `www`
3. **Type:** CNAME
4. **Value:** `d1o5b6xnnn6e9g.cloudfront.net`
5. **TTL:** 300
6. **Create**

---

## Step 6: Test

1. **Wait 10-15 minutes** (CloudFront + DNS)
2. **Clear browser cache** (Ctrl+Shift+Delete)
3. **Open incognito:** `https://www.venuehubpro.in`
4. **Should work!** 🎉

---

## Updating Your Site

### Upload to S3

```bash
aws s3 cp index.html s3://hall-manager-pro-marketing/ --acl public-read
```

Or: S3 Console → Upload → Replace file

### Clear CloudFront Cache

1. **CloudFront** → **Invalidations** → **"Create"**
2. **Paths:** `/*`
3. **Wait 2-5 minutes**

---

## Costs (Monthly)

| Service | Cost |
|---------|------|
| Domain | ₹50 |
| S3 | ₹2 |
| CloudFront | ₹3-5 |
| Route 53 | ₹50 |
| SSL (ACM) | FREE |
| **Total** | **₹105** |

---

## Troubleshooting

### 504 Gateway Timeout

**Fix:** CloudFront origin must be S3 **website endpoint** (not bucket):
- ✅ `bucket.s3-website.region.amazonaws.com`
- ❌ `bucket.s3.amazonaws.com`

### SSL Pending Validation

**Fix:** Use Route 53 "Create records" button in ACM

### Changes Not Visible

**Fix:** Create CloudFront invalidation `/*`

---

## Live URLs

- **Production:** https://www.venuehubpro.in
- **CloudFront:** https://d1o5b6xnnn6e9g.cloudfront.net
- **S3:** http://hall-manager-pro-marketing.s3-website.ap-south-1.amazonaws.com
- **GitHub:** https://github.com/lekshmimollyp-ops/hall-booking-marketing

---

**Deployment complete!** 🚀
