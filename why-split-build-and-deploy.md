# Why split build and deploy?

- The artifact becomes a first-class object
- You can redeploy without rebuilding
- Multiple deploy targets reuse the same build
- A failed build short-circuits before any deploy fires
