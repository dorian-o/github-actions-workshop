# AWS S3 + IAM setup (POC phase)

UI walkthrough for static website bucket + CI deploy user.

## 1. Create bucket

1. AWS Console → **S3** → **Create bucket**
2. **Bucket name**: globally unique, lowercase, no underscores (e.g. `dorian-fem-cicd-poc`)
3. **AWS Region**: pick closest (e.g. `eu-central-1`). Remember it — workflow needs it.
4. **Object Ownership**: ACLs disabled (recommended)
5. **Block Public Access settings**: **uncheck** "Block all public access"
   - Confirm warning checkbox: "I acknowledge..."
6. Versioning: Disabled (POC)
7. Default encryption: leave default (SSE-S3)
8. Click **Create bucket**

## 2. Enable static website hosting

1. Open bucket → **Properties** tab
2. Scroll to **Static website hosting** → **Edit**
3. Enable
4. Hosting type: **Host a static website**
5. **Index document**: `index.html`
6. **Error document**: `index.html` (SPA fallback) or `error.html`
7. Save changes
8. Copy **Bucket website endpoint** — public URL

## 3. Public-read bucket policy

1. **Permissions** tab → **Bucket policy** → **Edit**
2. Paste (replace `BUCKET_NAME`):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::BUCKET_NAME/*"
    }
  ]
}
```

3. Save

## 4. Test

Upload `index.html` manually via console → visit website endpoint → should render.

## 5. IAM user for CI

1. **IAM** → **Users** → **Create user**
2. Name: `fem-cicd-deployer`
3. **Do not** check "console access"
4. Permissions → **Attach policies directly** → **Create policy**:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:PutObject", "s3:DeleteObject", "s3:ListBucket", "s3:GetObject"],
      "Resource": [
        "arn:aws:s3:::BUCKET_NAME",
        "arn:aws:s3:::BUCKET_NAME/*"
      ]
    }
  ]
}
```

5. Create user → open user → **Security credentials** → **Create access key**
6. Use case: **Application running outside AWS**
7. Save **Access key ID** + **Secret access key** — secret shown only once.

> **Security:** access key + secret = long-lived creds. Treat like password. Add to GitHub repo secrets immediately, do not paste anywhere else.

## 6. GitHub repo secrets

Repo → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**:

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_S3_BUCKET` (bucket name)
- `AWS_REGION` (e.g. `eu-central-1`)

## Notes

- POC phase only — long-lived IAM keys are intentionally insecure. Stable phase will swap to OIDC + role assumption.
- Public-read bucket policy is workshop-only. Real apps front S3 with CloudFront + OAC.
