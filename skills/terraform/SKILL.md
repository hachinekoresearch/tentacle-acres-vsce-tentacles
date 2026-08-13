---
name: terraform
description: Safely author, review, validate, refactor, and troubleshoot Terraform and infrastructure-as-code changes.
---

# Terraform

Use this skill for Terraform and compatible IaC work. Prioritize safe, reviewed,
repeatable infrastructure changes over speed.

## Before making changes

- Read the repository's Terraform, backend, provider, workspace, and environment
  conventions. Reuse existing module and naming patterns.
- Determine the target environment and workspace explicitly. Do not rely on the
  current workspace, shell defaults, or a local state file.
- Treat state as production data. Never edit, delete, replace, move, import, or
  unlock state unless the operation is necessary, scoped, and understood.
- Obtain credentials through the approved identity, secret, and provider
  mechanisms. Do not print, commit, hard-code, or pass credentials in command
  arguments, plans, logs, examples, or generated files.
- Consult the exact provider and Terraform version documentation used by the
  repository before relying on resource arguments, defaults, lifecycle behavior,
  imports, upgrades, or deprecations.

## Authoring and refactoring

- Make the smallest coherent change. Preserve module interfaces and avoid
  unrelated formatting or resource churn.
- Use variables, outputs, locals, and modules consistently with existing code.
  Validate variable types and constraints at the boundary.
- Model dependencies through references rather than unnecessary `depends_on`.
  Use lifecycle rules only when their consequences are understood.
- Do not use provisioners or local-exec for ordinary provider capabilities.
- For resource renames or module moves, use explicit `moved` blocks where
  supported. For imports or state moves, document the exact resource address and
  verify it against the remote object before applying.
- Treat `count`, `for_each`, provider aliases, module paths, and resource
  addresses as state-sensitive. Review their effect on existing addresses before
  changing them.

## Reviewing plans

- Run `terraform fmt -check` and `terraform validate` in the relevant root
  module. Run `terraform init` with the repository's required backend settings;
  do not reconfigure or migrate a backend without explicit intent.
- Produce an environment-specific, saved plan using the selected workspace and
  approved variables. Review the saved plan before applying it; never apply an
  unreviewed fresh plan when a reviewed artifact is required.
- Inspect every create, update, replace, destroy, import, and move. Confirm
  identifiers, regions, accounts, projects, networks, IAM principals, and
  sensitive settings are correct.
- Treat any destroy or replacement as a stop-and-review event. Do not use
  `-auto-approve`, `-destroy`, `-replace`, targeted applies, or broad
  `-target` commands to bypass review. Use them only for a documented,
  narrowly scoped recovery procedure with explicit approval.
- Avoid applying plans from a workstation with ambiguous credentials or
  uncommitted configuration. Prefer the repository's approved CI/CD workflow.

## State and locking

- Use remote state and its normal locking mechanism whenever the repository
  supports it. Do not disable locking with `-lock=false`.
- If a lock is held, identify the active run and wait or coordinate with its
  owner. Force-unlock only after confirming the recorded lock is stale and that
  no apply is running.
- Back up or version state through its managed backend; do not copy state into
  source control, chat, tickets, or unsecured storage.
- Never run concurrent applies against the same state. Coordinate changes that
  share providers, state, or dependent infrastructure.

## Troubleshooting

- Start with non-mutating diagnostics: `terraform version`, `terraform
  providers`, `terraform workspace show`, `terraform state list`, and a
  read-only plan where appropriate.
- Compare configured provider versions, lock files, backend configuration,
  workspace selection, variables, and credentials with the known-good
  environment.
- Investigate drift with a plan and provider-native read-only checks. Do not
  suppress unexpected drift or use refresh-only or state operations as a
  substitute for understanding the change.
- Surface provider API errors, permission failures, locking conflicts, and
  state-address mismatches with their safe remediation. Avoid retries that can
  duplicate or partially create infrastructure.

## Completion criteria

Report the target workspace and environment, commands run, validation results,
reviewed plan summary, destructive or replacement actions, and any required
follow-up. Never claim an apply succeeded without confirming the recorded
outcome through the approved workflow or provider.
