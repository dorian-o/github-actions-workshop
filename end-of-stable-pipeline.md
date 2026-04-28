# End-of-Stable pipeline

```mermaid
flowchart LR
    PR[pull_request] --> CI[ci.yml<br/>uses _build.yml]
    CI -. required check .-> MAIN[(main protected)]
    MAIN --> PUSH[push: main]
    PUSH --> DEPLOY[deploy.yml<br/>build job uses _build.yml<br/>deploy job aws s3 sync]
    DEPLOY --> S3[(S3 bucket<br/>public-read)]
    CI -.-> BUILD[_build.yml]
    DEPLOY -.-> BUILD
    BUILD -.-> COMP[build-astro composite]
```

- `pull_request` triggers `ci.yml` which calls reusable `_build.yml`
- `ci.yml` exposes a `build` status check that branch protection requires before merge to `main`
- Push to protected `main` triggers `deploy.yml` — build job calls `_build.yml`, deploy job runs `aws s3 sync` to public-read S3 bucket
- Both `ci.yml` and `deploy.yml` reuse `_build.yml`, which itself wraps the `build-astro` composite action
