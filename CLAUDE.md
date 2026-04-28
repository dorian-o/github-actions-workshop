# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Context

FrontendMasters workshop: Cloud CI/CD with GitHub Actions. Reference repo: https://github.com/ALT-F4-LLC/fem-cicd-service (default branch `main`, verified live 2026-04-28). Workshop URL: https://frontendmasters.com/workshops/github-actions/.

Sample app upstream: **Astro** static site (`astro.config.mjs`, `src/`, `npm run build` → `dist/`).

Workshop arc — three stages, same `dist/` artifact, increasingly mature pipelines. Each stage has a `git checkout <branch>` end-state in upstream repo:

1. **POC** (branch `poc`, end of segment 6) — single `.github/workflows/deploy.yml`, two jobs: `build` (checkout, setup-node, `npm ci`, `npm run build`, upload artifact) + `deploy` (`needs: build`, download artifact, configure-aws-credentials from IAM-user keys in repo secrets, `aws s3 sync` to bucket). Triggers: `push` to `main`, `workflow_dispatch`. Public-read S3, `us-east-1` region in workflow. **Wrong on purpose:** long-lived AWS keys, no PR gate, no cache, no concurrency control, no CDN.

2. **Stable** (branch `stable`, end of segment 11) — adds composite action at `.github/actions/build-astro/action.yml` (checkout + setup-node w/ cache + install + build) and reusable workflow at `.github/workflows/_build.yml` (uploads `dist/`). `ci.yml` runs on PR (validates build), `deploy.yml` runs on push to `main`; both call `_build.yml`. Branch protection on `main`: required PR + required `build` status check. Optional `ACTIONS_STEP_DEBUG=true` repo secret for segment-8 debug demo. Same AWS topology as POC. **Still wrong:** long-lived keys, mutable `@v4` action tags, no env approval, no concurrency, public-read S3.

3. **Enterprise** (branch `enterprise`, end of segment 15) — every workflow file hardened: SHA-pinned third-party actions (CICD-SEC-3), top-level `permissions: {}` deny-all + per-job grants (CICD-SEC-1), `concurrency:` blocks (PR-keyed cancel for `ci.yml`, queued non-cancelling `production-deploy` group for `deploy.yml`), `production` environment with required reviewers + 1-min wait timer (30-min realistic). AWS auth via OIDC: `aws-actions/configure-aws-credentials` w/ `role-to-assume` against IAM role trust-bound to `repo:<owner>/<repo>:environment:production` (CICD-SEC-2/6). IAM user + AWS_ACCESS_KEY_ID/AWS_SECRET_ACCESS_KEY removed in segment 13. Repo **variable** `AWS_ROLE_TO_ASSUME` = role ARN. CloudFront + Origin Access Control fronts private S3 (no static hosting, no public read); deploy step also runs `cloudfront:CreateInvalidation`. Pre-create AWS OIDC provider at `arn:aws:iam::<ACCOUNT_ID>:oidc-provider/token.actions.githubusercontent.com`. Self-hosted runners discussed segment 15, **not implemented** in this branch.

Upstream branches: `main` (docs/default), `poc`, `stable`, `enterprise`, `feature/initial-implementation`.

Local working branch: `main` (workshop reference uses `feature/main`).

## Upstream reference docs (in `ALT-F4-LLC/fem-cicd-service`)

- `docs/instructor/OUTLINE.md` — master outline
- `docs/instructor/POC.md` / `STABLE.md` / `ENTERPRISE.md` — stage step-by-step guides
- `docs/tdd/fem-cicd-workshop-architecture.md` — architecture TDD (e.g. §6.4 OIDC trust policy)

## Repo Contents

Currently note-only. Markdown files transcribe workshop slides:

- `goals-of-course.md` — course goals + "same artifact, three pipelines" framing
- `course-structure.md` — three stages overview
- `pre-requisites.md` — GitHub, AWS free tier, Node v22
- `reference-repo.md` / `reference-branches.md` / `working-branch.md` — pointers to upstream workshop repo + branches
- `phase-scenario.md` — scenario framing (POC phase)
- `set-goals.md` — POC phase goals (one workflow, build, ship `dist/` to S3 on push to main)
- `phase-requirements.md` — POC phase prereqs (clean repo, S3 bucket, IAM user, repo secrets)
- `end-of-poc-pipeline.md` — diagram: push to main → build job → deploy job → S3 bucket
- `first-workflow.md` — workflow file basics
- `triggers-and-runners.md` / `triggers-we-will-use.md` — `on:` (when), `runs-on:` (where); push, workflow_dispatch, pull_request
- `contexts-and-expressions.md` — `${{ }}`, `if:`, contexts (github, runner, secrets, vars)
- `building-a-ci-pipeline.md` — checkout, setup-node, install + build
- `wrong-on-purpose.md` / `no-long-lived-credentials.md` — IAM key in repo secrets called out as deliberate POC anti-pattern
- `job-dependencies-and-artifacts.md` / `why-split-build-and-deploy.md` — upload/download-artifact, `needs:`
- `aws-s3-setup.md` — UI walkthrough: bucket, static hosting, public-read policy, IAM user, access key, GitHub secrets
- `schedule.md` — workshop time table (4:30pm–11:30pm)
- `.gitignore` — `videos/` (local screen recordings excluded)

Workflow YAML lands under `.github/workflows/` once exercises begin. Sample app (Astro) source not yet pulled into this workspace — clone or vendor from upstream `poc` branch when exercise reaches that step.

**Region:** `eu-north-1` (Stockholm). Override upstream `us-east-1` in any workflow copied from reference repo.

## Conventions for Slide Transcription

- One markdown file per slide topic, kebab-case filename matches user request.
- Preserve heading text from slide as `# H1`. Bullets verbatim.
- Inline code (`` `like-this` ``) for any monospace token shown on slide.
- Diagrams: render as mermaid `flowchart LR` plus a bullet breakdown beneath.

## Working Conventions

- Workshop = learning context. Minimal, illustrative changes. No production hardening, no extra lint/format/test infra unless step requires it.
- Workflow YAML: strict indentation. Quote `${{ ... }}` expressions when used as scalar values where YAML may misparse.
- Secrets: never inline. Reference `${{ secrets.NAME }}`. `GITHUB_TOKEN` auto-injected.
- Default runner: `ubuntu-latest` unless exercise specifies otherwise.

## Useful Commands (once workflows land)

- Lint workflow: `actionlint <file>`
- Run workflow locally: `act -j <job-id>`
- Trigger remote run: `gh workflow run <name>`
- Watch run: `gh run watch`
- Astro app (when source lands): `npm ci`, `npm run build` → `dist/`

## Notes

- Re-read this file's `## Repo Contents` section after new files added — keep index current.
- After workshop populates real source/workflows, replace `## Useful Commands` placeholders with concrete `package.json` scripts.
