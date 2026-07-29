# Golden Path implementation guide

## 1. One-time organization setup

Use `golden-path-workflows-v2` as the governance and provisioning repository.

1. Add the `GOLDEN_PATH_ADMIN_TOKEN` Actions secret to that repository. For
   production, prefer a GitHub App token with only the organization rules,
   custom-property, repository-administration, environment, and issue metadata
   permissions used by the workflows.
2. Run **Apply Golden Path Governance** once. It creates the organization custom
   properties and applies the three baseline organization rulesets.
3. The governance workflow creates or updates **Golden Path Security
   Baseline**. Provisioning attaches it to each enrolled repository, enabling
   CodeQL default setup, dependency graph, Dependabot alerts/security updates,
   secret scanning, and push protection where the organization license
   supports them.
4. Replace the sample CODEOWNER with approved teams.

Organization rulesets are the default source of truth. They target repositories
whose `golden_path` custom property is `enabled`, so a new application does not
need a copied repository ruleset.

## 2. Provision an application repository

Run **Provision Golden Path Repository** in `golden-path-workflows-v2`.

Required input:

- Repository name
- Description and visibility
- Release path: `eqa-only` or `eqa-epreprod`

Optional inputs:

- Shared-environment reviewer team ID
- Production reviewer team ID

The workflow creates the repository from this template and reconciles:

- Repository merge and Actions settings
- Standard labels
- `eint1`–`eint6`, `eqa`, `epreprod`, and `prod` Environments
- Deployment branch policies
- Golden Path custom properties
- Dependency vulnerability alerts and security updates

The operation is safe to rerun. Existing labels and environments are updated
instead of duplicated.

## 3. Adapt the generated repository

1. Replace `calculator.py` and the sample tests with the application.
2. Replace the commands in `scripts/` with real build, test, deployment, and
   verification commands. Keep the workflow job names stable because rulesets
   require the final gate checks.
3. Replace the sample CODEOWNER with the application team.
4. Keep the required output formats: JUnit XML for tests and Cobertura XML for
   coverage.

## 4. Branch and environment flow

| Branch | Action |
|---|---|
| `develop-*` | Build, unit test, quality, and security checks; no deployment |
| `feature-eintN-f###` | Build once, deploy to the named EINT environment, run integration/regression, report **INT Gate** |
| `release-eqa-*` / `hotfix-eqa-*` | Build once, deploy to EQA, test, report **Release Readiness** |
| `release-epreprod-*` / `hotfix-epreprod-*` | Build once, deploy/test in EQA, then deploy/test the same artifact in ePreProd, report **Release Readiness** |
| `main` | Deploy to production, smoke test, verify, then create the SemVer tag and GitHub Release |

Use pull requests for develop → feature → release → main. The release-to-main
pull request must have exactly one `major`, `minor`, or `patch` label.

## 5. Rulesets and stable checks

The organization rulesets require only checks that are guaranteed to report for
their target branch:

- Common: **Branch Name**, **Branch Flow**, **Coverage 80%**, and **Dependency Review**
- Feature → release: common checks plus **INT Gate**
- Release/hotfix → main: common checks plus **Release Readiness** and **Release Label**

CodeQL is enforced through the native code-scanning rule rather than a fragile
workflow job name. A repository ruleset is only needed as an explicit overlay
for a critical application with stricter approvals or additional controls.

## 6. POC evidence and limitations

Retain build packages, checksums, JUnit/Cobertura reports, deployment history,
test summaries, production verification, feature-ID tags, the final SemVer tag,
and the GitHub Release.

GitHub Actions artifacts stand in for Artifactory in this POC. The same artifact
is promoted from EQA to ePreProd within a release run. Because Actions artifacts
are run-scoped, the production run performs a new POC build on `main`; an
enterprise implementation should replace that boundary with immutable
Artifactory promotion.
