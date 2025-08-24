# Contributing to ClankerOS

Thank you for your interest in contributing to ClankerOS. This document outlines the workflow and Developer Certificate of Origin (DCO) requirements for all contributions.

## Workflow

1. **Plan**
   - Review existing Issues, RFCs, or ADRs and reference them before starting work.
2. **Branch**
   - Create a branch named `feature/w{W}-area/short-desc`.
3. **Develop & Test**
   - Work inside a Nix shell and run tests:
     ```
     nix develop -- bazel test //...
     ```
   - Run pre-commit checks before submitting:
     ```
     ./mk fmt
     ./mk lint
     ./mk hdl-lint
     ```
   - Update documentation so specs stay in sync with code.
4. **Open a Pull Request**
   - Push your branch and open a PR using the provided template.
   - Ensure CODEOWNERS approve impacted areas and keep history clean (squash or merge with DCO).
5. **CI**
   - If CI fails, fix issues and rerun until green before merge.

## Developer Certificate of Origin

All commits must be signed off to certify adherence to the Developer Certificate of Origin (DCO). Use:

```
git commit -s -m "Your commit message"
```

This adds a `Signed-off-by` line to your commit message and asserts that you have the right to submit your contribution under the project's license.

Please also review our [Code of Conduct](CODE_OF_CONDUCT.md) before participating.
