# Contexts & Expressions

- `${{ ... }}` is the only place YAML has logic
- Read who triggered the run, on what ref, from what event
- Gate steps with `if:`
- If you need real logic, write a script - not more `${{ }}`

## The contexts that matter today

- `github` - actor, ref, event_name, sha
- `runner` - OS, temp dir
- `secrets` - encrypted values (NOT readable in `if:`)
- `vars` - repo-level variables (readable everywhere)
