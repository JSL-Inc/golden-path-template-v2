# Golden Path application template v2 — simplified version 1

Minimal application starter for the GitLab-to-GitHub migration POC.

The application owns code, tests, and five adapter scripts. Central workflows
own orchestration. GitHub organization settings own security and quality
enforcement.

## Repository-owned files

- `.github/workflows/pull-request.yml`
- `.github/workflows/delivery.yml`
- optional `.github/workflows/dast.yml`
- five `.github/golden-path/` adapter scripts
- `CODEOWNERS`, Dependabot configuration, application code, and tests

## Standard evidence

`ci.sh` must create:

- `reports/junit/results.xml`
- `reports/coverage/cobertura.xml`
- at least one immutable build file under `dist/`

Coverage is uploaded to GitHub Code Quality and the organization ruleset owns
the 80% merge threshold.

See [architecture](docs/architecture.md), [GitHub settings](docs/github-settings.md),
and the [demo](docs/demo.md).
