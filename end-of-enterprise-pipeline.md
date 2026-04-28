# End-of-Enterprise pipeline

```mermaid
flowchart LR
    PR[pull_request] --> CI[ci.yml<br/>SHA-pinned<br/>concurrency: pr-#]
    CI -. required check .-> MAIN[(main protected)]
    MAIN --> PUSH[push: main]
    PUSH --> DEPLOY[deploy.yml<br/>environment: production<br/>concurrency: prod-deploy<br/>id-token: write]
    DEPLOY --> OIDC[OIDC token exchange<br/>sts:AssumeRoleWithWebIdentity]
    OIDC --> ROLE[IAM Role<br/>least-privilege]
    ROLE --> S3[(S3 private)]
    S3 --> CF[CloudFront<br/>OAC + Invalidation]
    CF --> USERS[End users]
```

- PR-triggered `ci.yml` pins every action to a commit SHA, runs under PR-keyed concurrency that cancels superseded runs
- Branch protection on `main` requires the `build` check to pass before merge
- Push to `main` runs `deploy.yml` against the `production` environment (required reviewers + wait timer), under a queued non-cancelling `prod-deploy` concurrency group
- `id-token: write` lets the runner request a GitHub OIDC token; `sts:AssumeRoleWithWebIdentity` exchanges it for short-lived AWS credentials on a least-privilege IAM role
- Role pushes the artifact to a private S3 bucket and invalidates CloudFront
- CloudFront with Origin Access Control fronts the bucket — end users hit the CDN, never S3 directly
