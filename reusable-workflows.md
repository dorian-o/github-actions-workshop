# Reusable Workflows

- A reusable workflow wraps a whole job (or jobs)
- Triggered by `on: workflow_call`
- Runs as its OWN job with its OWN runner
- Composite is step-level; reusable is job-level
