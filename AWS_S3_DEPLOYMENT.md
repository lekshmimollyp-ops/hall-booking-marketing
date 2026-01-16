# AWS S3 Deployment Guide - Hall Manager Pro

Complete guide to deploy the Hall Manager Pro marketing landing page to AWS S3 with CloudFront CDN.

---

## Prerequisites

- AWS Account
- AWS CLI installed (optional, but recommended)
- Your landing page files ready

---

## Step 1: Create S3 Bucket

### Via AWS Console

1. **Login to AWS Console**
   - Go to https://console.aws.amazon.com
   - Navigate to **S3** service

2. **Create Bucket**
   - Click **"Create bucket"**
   - **Bucket name**: `hall-manager-pro-marketing` (must be globally unique)
   - **Region**: Choose closest to your target audience (e.g., `ap-south-1` for India)
   - **Uncheck** "Block all public access"
   - ✅ Check "I acknowledge that the current settings might result in this bucket and the objects within becoming public"
   - Click **"Create bucket"**

---

## Step 2: Upload Files

### Option A: AWS Console (Easy)

1. Click on your bucket name
2. Click **"Upload"**
3. Click **"Add files"**
4. Select `index.html` from your local folder
5. Click **"Upload"**

### Option B: AWS CLI (Faster)

```bash
# Navigate to your project folder
cd "c:\Users\Lekshmi M\Documents\velosit\retalinepro\static-websites\hall-booking-marketing"

# Upload files
aws s3 sync . s3://hall-manager-pro-marketing --exclude ".git/*"
```

---

## Step 3: Enable Static Website Hosting

1. Go to your bucket
2. Click **"Properties"** tab
3. Scroll to **"Static website hosting"**
4. Click **"Edit"**
5. Select **"Enable"**
6. **Index document**: `index.html`
7. **Error document**: `index.html` (for SPA routing)
8. Click **"Save changes"**
9. **Note the endpoint URL** (e.g., `http://hall-manager-pro-marketing.s3-website.ap-south-1.amazonaws.com`)

---

## Step 4: Set Bucket Policy (Make Public)

1. Go to **"Permissions"** tab
2. Scroll to **"Bucket policy"**
3. Click **"Edit"**
4. Paste this policy (replace `YOUR-BUCKET-NAME`):

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::hall-manager-pro-marketing/*"
        }
    ]
}
```

5. Click **"Save changes"**

---

## Step 5: Test Your Website

Open the S3 website endpoint in your browser:
```
http://hall-manager-pro-marketing.s3-website.ap-south-1.amazonaws.com
```

✅ Your landing page should be live!

---

## Step 6: Add CloudFront CDN (Recommended)

CloudFront provides:
- ✅ HTTPS support
- ✅ Faster global delivery
- ✅ Custom domain support
- ✅ Better SEO

### Setup CloudFront

1. Go to **CloudFront** service in AWS Console
2. Click **"Create Distribution"**
3. **Origin Settings:**
   - **Origin domain**: Select your S3 bucket from dropdown
   - **Origin path**: Leave empty
   - **Name**: Auto-filled

4. **Default Cache Behavior:**
   - **Viewer protocol policy**: Redirect HTTP to HTTPS
   - **Allowed HTTP methods**: GET, HEAD
   - **Cache policy**: CachingOptimized

5. **Settings:**
   - **Price class**: Use all edge locations (or choose based on budget)
   - **Alternate domain names (CNAMEs)**: Add your custom domain (optional)
   - **Default root object**: `index.html`

6. Click **"Create distribution"**
7. **Wait 5-10 minutes** for deployment
8. **Note the CloudFront domain** (e.g., `d1234abcd.cloudfront.net`)

---

## Step 7: Configure Custom Domain (Optional)

### If you have a domain (e.g., hallmanagerpro.com):

1. **Request SSL Certificate** (AWS Certificate Manager):
   - Go to **Certificate Manager** (must be in `us-east-1` region for CloudFront)
   - Click **"Request certificate"**
   - Choose **"Request a public certificate"**
   - Enter domain: `hallmanagerpro.com` and `www.hallmanagerpro.com`
   - Validation: Choose **DNS validation**
   - Add CNAME records to your domain registrar
   - Wait for validation (5-30 minutes)

2. **Update CloudFront Distribution**:
   - Edit your distribution
   - **Alternate domain names**: Add `hallmanagerpro.com`
   - **SSL certificate**: Select your ACM certificate
   - Save changes

3. **Update DNS**:
   - Go to your domain registrar (GoDaddy, Namecheap, etc.)
   - Add CNAME record:
     - Name: `www`
     - Value: Your CloudFront domain (e.g., `d1234abcd.cloudfront.net`)
   - Add A record (or ALIAS if Route 53):
     - Name: `@` (root domain)
     - Value: CloudFront domain

---

## Step 8: Update Files (Future Updates)

### When you update your landing page:

**Option A: AWS Console**
1. Go to S3 bucket
2. Upload new `index.html` (overwrite existing)
3. Invalidate CloudFront cache (see below)

**Option B: AWS CLI**
```bash
# Upload updated file
aws s3 cp index.html s3://hall-manager-pro-marketing/

# Invalidate CloudFront cache
aws cloudfront create-invalidation --distribution-id YOUR_DISTRIBUTION_ID --paths "/*"
```

### CloudFront Cache Invalidation

After updating files, clear CloudFront cache:

1. Go to **CloudFront** → Your distribution
2. Click **"Invalidations"** tab
3. Click **"Create invalidation"**
4. Enter: `/*`
5. Click **"Create invalidation"**
6. Wait 2-5 minutes

---

## Cost Estimate (India Region)

### S3 Storage
- First 50 TB: $0.023 per GB/month
- Your site (~28 KB): **~$0.001/month** (negligible)

### S3 Requests
- GET requests: $0.0004 per 1,000 requests
- 10,000 visitors/month: **~$0.004/month**

### CloudFront (Optional)
- First 10 TB: $0.085 per GB
- 10,000 visitors (~280 MB): **~$0.024/month**

**Total: ~$0.03/month** (₹2-3/month) 🎉

---

## Security Best Practices

1. **Enable S3 Versioning**:
   - Bucket → Properties → Versioning → Enable
   - Protects against accidental deletions

2. **Enable CloudFront Logging**:
   - Track visitor analytics
   - Monitor for issues

3. **Set Cache Headers**:
   - Add metadata to `index.html`:
     - `Cache-Control`: `max-age=3600` (1 hour)

---

## Monitoring

### CloudWatch Metrics (Free)
- S3 bucket metrics
- CloudFront request count
- Error rates

### Google Analytics (Recommended)
Add to `index.html` before `</head>`:

```html
<!-- Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

---

## Troubleshooting

### Issue: 403 Forbidden
- **Fix**: Check bucket policy is set correctly
- Ensure "Block public access" is OFF

### Issue: 404 Not Found
- **Fix**: Verify `index.html` is in bucket root
- Check static website hosting is enabled

### Issue: Form not submitting
- **Fix**: Must access via `https://` (not `file://`)
- CloudFront provides HTTPS automatically

### Issue: Changes not visible
- **Fix**: Clear CloudFront cache (invalidation)
- Hard refresh browser (Ctrl+Shift+R)

---

## Quick Deployment Checklist

- [ ] Create S3 bucket
- [ ] Upload `index.html`
- [ ] Enable static website hosting
- [ ] Set bucket policy (public read)
- [ ] Test S3 endpoint
- [ ] Create CloudFront distribution
- [ ] Wait for CloudFront deployment
- [ ] Test CloudFront URL
- [ ] (Optional) Add custom domain
- [ ] (Optional) Set up SSL certificate
- [ ] Test form submission
- [ ] Add to GitHub README

---

## Final URLs

After deployment, you'll have:

1. **S3 Endpoint** (HTTP only):
   ```
   http://hall-manager-pro-marketing.s3-website.ap-south-1.amazonaws.com
   ```

2. **CloudFront URL** (HTTPS):
   ```
   https://d1234abcd.cloudfront.net
   ```

3. **Custom Domain** (if configured):
   ```
   https://hallmanagerpro.com
   ```

---

## Support

For AWS-specific issues:
- AWS Documentation: https://docs.aws.amazon.com/s3/
- AWS Support: https://console.aws.amazon.com/support/

For Hall Manager Pro landing page issues:
- GitHub: https://github.com/lekshmimollyp-ops/hall-booking-marketing

---

**Your landing page is ready to deploy to AWS S3!** 🚀

Start with Step 1 and work through each step. The entire process takes about 15-20 minutes.
