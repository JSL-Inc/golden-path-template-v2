# Golden Path demonstration

## Before the meeting

1. Provision the demo repository with **Provision Golden Path Repository**.
2. Select either `eqa-only` or `eqa-epreprod`.
3. Confirm the organization code-security configuration is attached.
4. Create `release-eqa-poc-release` or `release-epreprod-poc-release`, then
   `feature-eint1-f26`, then `develop-s34`.

Provisioning already creates the Environments, labels, repository settings, and
custom-property enrollment used by the organization rulesets.

## 1. Story development

Push an application and unit-test change to `develop-s34`.

- Build, unit test, code quality, coverage, and security checks run.
- JUnit XML, Cobertura XML, and the build artifact are retained.
- No deployment runs for a develop branch.

Open `develop-s34 → feature-eint1-f26`. The branch and pull-request checks run
once and enforce the transition.

## 2. Feature integration

Merge into `feature-eint1-f26`.

- The application is built once.
- The artifact deploys to `eint1`.
- Integration and regression tests run after deployment.
- **INT Gate** summarizes the result.

Open `feature-eint1-f26 → release-...`. The organization ruleset requires the
common checks and **INT Gate**.

## 3. Release candidate

Merge into the release branch.

- **Feature Tagging** extracts `f26` from the source branch and tags the release
  merge commit.
- For `release-eqa-*`, the artifact deploys and is tested in EQA.
- For `release-epreprod-*`, that same artifact deploys and is tested in EQA,
  then is promoted to ePreProd and tested again.
- **Release Readiness** reports one final, stable status for the selected path.

Open the release branch → `main`, add exactly one `major`, `minor`, or `patch`
label, obtain the required approvals, and merge.

## 4. Production and official release

The main-branch pipeline:

1. Builds the POC production artifact.
2. Deploys through the protected `prod` Environment.
3. Smoke tests and verifies production.
4. Calculates the next SemVer from the merged PR label.
5. Creates the `vMAJOR.MINOR.PATCH` tag and GitHub Release with the artifact and
   verification evidence.

The separate release workflow waits for the successful production delivery run;
it does not start a second deployment.

## What to point out

- One organization policy enrolls repositories by the `golden_path=enabled`
  property.
- Provisioning makes repository setup repeatable rather than manual.
- EQA-only and EQA→ePreProd applications share one template.
- Rulesets require final gates rather than every conditional job, avoiding a
  page of skipped or permanently expected checks.
- Application teams edit local YAML and the commands in `scripts/`; they do not
  need to understand a caller repository.

## Expected POC limitation

The release candidate is reused between EQA and ePreProd. The main production
run rebuilds because GitHub Actions artifacts are scoped to workflow runs.
Replacing that boundary with Artifactory promotion is the production hardening
step, not a POC prerequisite.
