# Why a composite for the build sequence

- setup-node + install + build + upload-artifact = one logical unit
- Future workflows in this repo will call the same composite
- Local composites can't bootstrap their own checkout
- Checkout stays in the calling job - every time
