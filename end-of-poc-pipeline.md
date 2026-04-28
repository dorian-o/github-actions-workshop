# End-of-POC pipeline

```mermaid
flowchart LR
    A[push to main] --> B["build job<br/>checkout + setup-node<br/>npm ci + npm run build<br/>upload-artifact"]
    B --> C["deploy job<br/>download-artifact<br/>configure-aws-credentials<br/>(IAM key)<br/>aws s3 sync"]
    C --> D[(S3 bucket<br/>public-read)]
```

## Stages

1. **push to main** — trigger
2. **build job**
   - checkout + setup-node
   - npm ci + npm run build
   - upload-artifact
3. **deploy job**
   - download-artifact
   - configure-aws-credentials (IAM key)
   - aws s3 sync
4. **S3 bucket** (public-read)
