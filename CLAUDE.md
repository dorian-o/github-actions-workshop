# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Context

FrontendMasters workshop: Cloud CI/CD with GitHub Actions. Workspace currently empty — files added as workshop progresses (exercises, sample apps, workflow YAML).

## Expected Layout (once populated)

- `.github/workflows/` — workflow files (`*.yml`). Primary artifacts of workshop.
- Sample app code (likely Node.js/JS based on FrontendMasters norm) at root or under `app/`.
- Exercise branches or numbered folders (e.g. `01-basics`, `02-matrix`) common in FM workshops.

## Working Conventions

- Workshop = learning context. Prefer minimal, illustrative changes over production hardening. Avoid adding lint/format/test infra unless workshop step requires it.
- When editing workflows: validate YAML indentation strictly (GitHub Actions parser strict). Quote expressions `${{ ... }}` when used as scalar values where YAML may misparse.
- Secrets: never inline. Reference via `${{ secrets.NAME }}`. `GITHUB_TOKEN` auto-injected — no manual creation.
- Default runner: `ubuntu-latest` unless exercise specifies otherwise.

## Commands (placeholders — fill once stack known)

- Run workflow locally: `act` (if installed) — `act -j <job-id>`.
- Lint workflow: `actionlint <file>`.
- Trigger workflow remotely: `gh workflow run <name>`.
- Watch run: `gh run watch`.

## Notes for Future Sessions

- Re-run `/init` after repo populated to replace this stub with real architecture notes.
- Workshop instructor: check FrontendMasters course page for module order if branch names ambiguous.
