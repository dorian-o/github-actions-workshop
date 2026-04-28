# Phase Goals

## POC

- One workflow file in the repo
- Build the frontend in CI
- Ship `dist/` to S3 on push to `main`

## Stable

- Cache npm so installs finish in seconds
- Validate pull requests before they merge
- Extract the build sequence to ONE place
- Protect `main` so direct pushes are blocked

## Enterprise

- Replace long-lived AWS keys with OIDC
- Gate the deploy on a human
- Pin actions to commit SHAs
- Front S3 with CloudFront + OAC
- Add concurrency control
