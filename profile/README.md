# arcanelabsio

An independent software studio. We ship focused tools — for personal health, family finance, developer productivity, and the AI-assisted engineering workflow we use ourselves.

---

## What we build

**[Longeviti](https://longeviti.app)** — nutrition coaching, grounded in your health reports. A practitioner-led platform for certified nutritionists and their clients. _Rebuilding the client-facing platform in 2026; [join the waitlist](https://longeviti.app)._

**Vael** — a local-first family finance app for Indian households. Encrypted client-side, synced through Google Drive as blind ciphertext transport — Vael's servers (there are none) literally cannot read your data. _Source currently private — for access, email [support@arcanelabs.info](mailto:support@arcanelabs.info)._

**[Forge](https://github.com/arcanelabsio/forge)** — a CLI that gives AI coding assistants superpowers over GitHub workflows. One install plugs into Copilot, Claude Code, Codex, and Gemini CLI — PR reviews, discussion analysis, commit coaching.

**[Shellcraft](https://github.com/arcanelabsio/shellcraft)** — a macOS dev machine bootstrapper. Profile-based package selection and non-destructive dotfile management so you can rebuild your workstation in an hour instead of a weekend.

**[cloud_sync](https://github.com/arcanelabsio/cloud_sync)** — a Dart/Flutter monorepo for bidirectional file sync across cloud backends. A storage-agnostic core ([`cloud_sync_core`](https://pub.dev/packages/cloud_sync_core)) plus per-backend adapters for Google Drive ([`cloud_sync_drive`](https://pub.dev/packages/cloud_sync_drive)), AWS S3 + S3-compatibles ([`cloud_sync_s3`](https://pub.dev/packages/cloud_sync_s3)), and Box ([`cloud_sync_box`](https://pub.dev/packages/cloud_sync_box)). Supersedes `drive_sync_flutter`.

**[drive_sync_flutter](https://github.com/arcanelabsio/drive_sync_flutter)** — _frozen at [1.2.0](https://pub.dev/packages/drive_sync_flutter)._ The single-package Google Drive predecessor. New development and new backends live in [`cloud_sync`](https://github.com/arcanelabsio/cloud_sync); see [`cloud_sync_drive`](https://pub.dev/packages/cloud_sync_drive) for the Drive adapter going forward.

---

## How we work

Contract-first schemas, ADR-driven decisions, small surface area per product, AI-augmented where it helps. Each product picks its own architecture — we don't force a single playbook across the portfolio. Engineering patterns and detailed architecture notes live in internal repositories.

Starter template for new Arcane Labs repos: [**arcanelabsio/repo-template**](https://github.com/arcanelabsio/repo-template) (AGENTS.md, ADR scaffold, SECURITY_MODEL, Conventional Commits).

---

## Who's behind this

Solo shop, for now. Built by [@ajitgunturi](https://github.com/ajitgunturi) — R&D engineer, platform & control-plane background, ISB Emerging Leaders alum. If any of this resonates, [let's connect on LinkedIn](https://www.linkedin.com/in/ajitkumargunturi/).

Contributions welcome on the open-source repos (`forge`, `shellcraft`, `cloud_sync`). See each project's `CONTRIBUTING.md`.
