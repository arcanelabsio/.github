# Security Policy

## Reporting a Vulnerability

If you believe you've found a security vulnerability in any Arcane Labs project, please **do not open a public issue**. Instead, report it privately so we can address it before it's disclosed.

### How to report

Email **security@arcanelabs.info** with:

- The project and version affected
- A description of the vulnerability
- Steps to reproduce (proof-of-concept welcome)
- Your assessment of impact

You'll receive an acknowledgement within **3 business days**. We aim to provide a status update within **7 business days** and a fix or mitigation plan within **30 days** for confirmed issues.

### Scope

This policy applies to all repositories under the [`arcanelabsio`](https://github.com/arcanelabsio) organization, including:

- **Longeviti** — app, framework, and documentation site
- **Vael** — family finance app
- **Forge** — AI plugin CLI
- **drive_sync_flutter** — Google Drive sync library
- **Shellcraft** — macOS workstation bootstrapper

### Out of scope

- Issues in third-party dependencies (report upstream first; we'll coordinate if needed)
- Social engineering, physical attacks, and non-technical issues
- Issues requiring privileged access to a user's device or Google Drive

### Disclosure

We practice coordinated disclosure. Once a fix is released, we'll publish an advisory on the affected repository. Reporters who follow this policy are credited (unless anonymity is preferred).

---

*This policy serves as the default for all `arcanelabsio` repositories. Individual projects may override it with a repo-local `SECURITY.md`.*
