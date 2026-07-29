# Organization onboarding

New repositories should be created with **Provision Golden Path Repository**
in `JSL-Inc/golden-path-workflows-v2`.

That workflow:

1. creates the repository from this template;
2. enables squash merges and automatic branch cleanup;
3. creates `major`, `minor`, and `patch` labels;
4. creates `eint1`-`eint6`, `eqa`, `epreprod`, and `prod`;
5. restricts each environment to its approved branch patterns;
6. enrolls the repository in the organization rulesets; and
7. enables vulnerability alerts and Dependabot security updates.

The organization code-security configuration supplies CodeQL default setup,
secret scanning, push protection, dependency graph, and Dependabot settings.
They are GitHub settings, not duplicated workflow YAML.

## Release paths

- `release-eqa-*` and `hotfix-eqa-*`: EQA, QA Gate, then production after the
  approved merge to `main`.
- `release-epreprod-*` and `hotfix-epreprod-*`: EQA, QA Gate, ePreProd,
  ePreProd Gate, then production after the approved merge to `main`.

Both paths use the artifact created by the same release-branch workflow run.
For this POC it is stored as a GitHub Actions artifact. A production rollout
should replace that storage boundary with the approved artifact repository.

## Required checks

Organization rulesets require only stable, always-applicable checks:

- `Branch Validation / Branch Name`
- `PR Flow / Branch Flow`
- `Code Coverage / Coverage 80%`
- `Security Review / Dependency Review`
- `Branch Delivery Pipeline / INT Gate` on release branches
- `Branch Delivery Pipeline / Release Readiness` on `main`
- `PR SemVer Check / Release Label` on `main`

Individual deployment and test jobs are intentionally not required. They can
be skipped when a branch does not use that route; the final gate summarizes the
applicable child jobs.
