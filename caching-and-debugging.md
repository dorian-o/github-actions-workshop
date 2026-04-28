# Caching & Debugging

- Cache is the highest-leverage change you can make
- `setup-node` with `cache: 'npm'`, short and hard to misconfigure
- `actions/cache` - when you need something setup-node doesn't know about
- `ACTIONS_STEP_DEBUG=true` is the equivalent of `set -x`
