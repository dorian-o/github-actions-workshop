# Why deploys never cancel

- A deploy mid-sync leaves S3 and CloudFront inconsistent
- You don't want to find out at 4 PM Friday that half your assets are old
- Cancellation is safe when work is idempotent
- It's unsafe when work has side effects partway through
