# AGENTS.md

## Scope
These instructions apply to the entire repository.

## Using the Algorithm
- Review `README.md` and `ALGORITHM.md` to understand project goals and workflow.
- Follow the algorithm's **Daily Dev Loop**:
  - Open or reference an Issue, RFC, or ADR before starting work.
  - Create a branch using the convention `feature/w{W}-area/short-desc`.
  - Develop and test within a Nix shell: `nix develop -- bazel test //...` (scoped to affected targets).
  - Run pre-commit checks: `./mk fmt && ./mk lint && ./mk hdl-lint`.
  - Update documentation so specs stay in sync with code.
  - Push the branch and open a PR using the template, ensuring CODEOWNERS approve impacted areas.
  - If CI fails, fix issues and rerun until green before merge.
- Squash or merge commits using DCO and keep history clean.
- Store large artifacts outside of git per data policy and use artifacts storage for hardware outputs.

