# Governance source

This repository intentionally contains no default repository rulesets.

Normal Golden Path repositories are enrolled in the organization rulesets by
the custom property `golden_path=enabled`. The specifications and provisioning
workflows live in `JSL-Inc/golden-path-workflows-v2`.

Use a repository ruleset only when an application has a documented requirement
that is stricter than the organization baseline. Do not copy the organization
rulesets into this template; copied policies drift and make bulk changes
manual.
