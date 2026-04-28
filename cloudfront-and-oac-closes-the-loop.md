# CloudFront + OAC closes the loop

- S3 flips from public-read to private
- Only the OAC service principal can read objects
- CloudFront becomes the public surface
- The deploy job invalidates the cache after sync
