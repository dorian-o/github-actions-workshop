# Job Dependencies & Artifacts

- Real pipelines split build from deploy
- Each job runs on a fresh runner - no shared filesystem
- `upload-artifact` / `download-artifact` move the bytes
- `needs:` declares the ordering
