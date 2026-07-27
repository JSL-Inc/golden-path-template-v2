# Golden Path Application Template v2

Runnable GitHub Enterprise Cloud proof-of-concept template based on the supplied COUNTRY standards.

## Included controls

- COUNTRY branch flow: `main → release → feature → develop`
- Pull requests for `develop → feature → release → main`
- Automatic `f###` traceability tag when a feature PR merges into a release branch
- Hotfix flow: `main → hotfix → main → release → feature`
- JUnit XML test evidence
- Cobertura XML line coverage
- Blocking 80% coverage baseline with an approved transition mode
- Tests before build for fail-fast feedback
- Blocking build and code-quality checks
- Optional CodeQL and dependency review, plus Dependabot and secret-protection guidance
- OWASP ZAP DAST against non-production targets
- Build-once artifact promotion through `eint1`–`eint6`, `eqa`, `epreprod`, and `prod`
- Semantic versioning and verified release creation
- Production verification and rollback guidance
- API-ready ruleset and environment specifications

## Quick start

```bash
python -m pip install -r requirements.txt
bash .github/golden-path/unit-test.sh
bash .github/golden-path/build.sh
bash .github/golden-path/lint.sh
```

## Automatic demonstration flow

1. Create `release-eqa-poc-release` from `main`.
2. Create `feature-eint1-f26` from the release branch.
3. Create `develop-s34` from the feature branch.
4. Push application changes to `develop-s34`; one CI run builds the evidence and artifact.
5. Open PRs for `develop → feature → release → main`.
6. Merging `feature-eint1-f26` into the release branch automatically tags that release-branch merge commit as `f26`.
7. A feature push automatically validates and deploys to its named `eint1`–`eint6`.
8. A release push automatically validates once, then deploys the same artifact to the environment named by the branch: `release-eqa-*` goes to EQA and `release-epreprod-*` goes to ePreProd.
9. Add exactly one `major`, `minor`, or `patch` label to the PR entering `main`.
10. After merge, the verified release artifact is automatically promoted to `prod`.
11. Production verification automatically creates the matching SemVer tag and GitHub Release.

Normal pushes do not also start a second PR copy of CI. PR events run only the
branch/release policy and the optional security workflow. Manual workflows are
reserved for recovery, rollback, DAST, system, and performance demonstrations.

Start with [docs/executive-summary.md](docs/executive-summary.md), then use
[docs/architecture.md](docs/architecture.md),
[docs/implementation-guide.md](docs/implementation-guide.md), and
[docs/demo-plan.md](docs/demo-plan.md).

The complete design documentation is in [`docs/`](docs/).
