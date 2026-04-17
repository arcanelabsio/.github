# Contributing to Arcane Labs Projects

Thanks for your interest in contributing. We build software where *your* data never leaves *your* infrastructure — contributions that push that thesis forward are welcome.

## Before you start

- **Read the project's own docs first.** Many repos have a repo-local `CONTRIBUTING.md`, `AGENTS.md`, or `CLAUDE.md` with project-specific guidance. That takes precedence over this document.
- **Check open issues and discussions.** Someone may already be working on it. A quick comment saves duplicate effort.
- **For large changes, open an issue first.** Discuss the approach before you invest time. Small bug fixes can go straight to PR.

## How to contribute

### Reporting bugs

Open an issue using the bug-report template (if the repo provides one). Include:

- What you expected to happen
- What actually happened
- Steps to reproduce
- Versions (OS, runtime, package version)

### Proposing features

Open an issue using the feature-request template. Describe the use case first, then the proposed solution. We optimize for user-owned data and zero-server architectures — proposals that keep those constraints intact get priority.

### Submitting code

1. **Fork** the repository and create a feature branch (`feat/my-change` or `fix/some-bug`).
2. **Make your changes.** Keep commits atomic and meaningful. Follow [Conventional Commits](https://www.conventionalcommits.org) — `feat:`, `fix:`, `docs:`, `chore:`, `refactor:`, `test:`, `ci:`.
3. **Write tests** when behavior changes. CI must pass before review.
4. **Open a PR** against `main`. Use the PR template. Link the issue it resolves.
5. **Respond to review feedback.** Rebase rather than merge when syncing with main (our repos enforce linear history).

### Commit style

- `feat(scope): add new behavior`
- `fix(scope): correct existing behavior`
- `docs(scope): documentation only`
- `refactor(scope): restructure without behavior change`
- `test(scope): add or update tests`
- `chore(scope): tooling, deps, no-op`
- `ci(scope): pipeline changes`

Scope is optional but helpful (e.g., `feat(atlas):`, `fix(ui):`).

## Review & merge

- **Protected main.** Direct pushes are blocked on all public repos. Changes merge via PR using squash or rebase (we enforce linear history).
- **Review turnaround.** We aim for a first response within 3 business days.
- **Admin override.** Maintainers may bypass protection for genuine emergencies — we try not to.

## Licensing

By contributing, you agree your contributions will be licensed under the project's existing license (typically MIT; check the repo's `LICENSE`).

## Code of Conduct

Participation is governed by our [Code of Conduct](./CODE_OF_CONDUCT.md). Report issues to **community@arcanelabs.info**.

## Questions

- **General**: open a discussion if the repo has them enabled, or email **apps@arcanelabs.info**
- **Security**: see [SECURITY.md](./SECURITY.md) — do not open public issues for vulnerabilities
- **Support**: see [SUPPORT.md](./SUPPORT.md)

---

*This guide serves as the default for all `arcanelabsio` repositories. Individual projects may override it with a repo-local `CONTRIBUTING.md`.*
