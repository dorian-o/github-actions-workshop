# Permissions: deny-all by default

- Workflow level: `permissions: {}`
- Each job grants ONLY what it needs
- `contents: read` for jobs that checkout
- `id-token: write` ONLY on the deploy job
