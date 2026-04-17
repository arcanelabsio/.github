# arcanelabsio

An independent software studio. We ship focused tools — for personal health, family finance, developer productivity, and the AI-assisted engineering workflow we use ourselves.

> **How we work:** contract-first schemas, ADR-driven decisions, small surface area per product, AI-augmented where it helps. Each product has its own architecture chosen for its problem — we don't force a single playbook across the portfolio.

---

## What we build

**[Longeviti](https://longeviti.app)** — nutrition coaching, grounded in your health reports. A practitioner-led platform for certified nutritionists and their clients. _Rebuilding the client-facing platform in 2026; [join the waitlist](https://longeviti.app)._

**Vael** — a local-first family finance app for Indian households. Encrypted client-side, synced through Google Drive as blind ciphertext transport — Vael's servers (there are none) literally cannot read your data. Flutter + Drift + client-side AES-GCM. 400+ tests, TDD from day one. _Source currently private — for access, email [support@arcanelabs.info](mailto:support@arcanelabs.info)._

**[Forge](https://github.com/arcanelabsio/forge)** — a CLI that gives AI coding assistants superpowers over GitHub workflows. One install plugs into Copilot, Claude Code, Codex, and Gemini CLI — PR reviews, discussion analysis, commit coaching. Zero lock-in to any provider.

**[Shellcraft](https://github.com/arcanelabsio/shellcraft)** — a macOS dev machine bootstrapper. Profile-based package selection and non-destructive dotfile management so you can rebuild your workstation in an hour instead of a weekend.

**[drive_sync_flutter](https://github.com/arcanelabsio/drive_sync_flutter)** — a pub.dev library for bidirectional Google Drive sync with pluggable OAuth scope modes (full `drive`, `drive.file`, `drive.appdata`). Used by Vael + available for any Flutter app that needs Drive as a data layer.

---

## How we ship it — the architecture

A small set of org-wide engineering patterns: contract-first schemas, planner/executor split, managed-block installs, ADR discipline, live-fetch read-only execution, AGENTS.md as the authoritative AI-assistant guide. Each pattern has a canonical implementation you can read.

→ **[ARCHITECTURE.md](./ARCHITECTURE.md)** — org-wide patterns, where each one is most faithfully implemented, and the two-tier governance (patterns in [`arcanelabsio-patterns`](https://github.com/arcanelabsio/arcanelabsio-patterns), decisions in each project's `docs/adr/`).

→ **[arcanelabsio/repo-template](https://github.com/arcanelabsio/repo-template)** — click "Use this template" to start a new Arcane Labs repo pre-wired with the conventions above (AGENTS.md, ADR scaffold, SECURITY_MODEL, Conventional Commits).

---

## How we ship

- **Small surface per product.** One thing done well beats five things half-done. Each product is scoped so a small team can ship it and support it.
- **Contracts before code.** Schemas, interfaces, and ADRs come first; implementations follow. Forking stays safe because the contract is the thing, not the code.
- **AI-augmented, not AI-dependent.** We use AI assistants heavily in our own workflow, and we build tooling that assumes AI is part of the toolkit (Forge, Longeviti's practitioner workflows). Products degrade gracefully without it.
- **Documented decisions.** Every non-trivial choice lives as an ADR in the project's `docs/adr/`. Future us (and anyone reading the code) gets the *why*, not just the *what*.

---

## Who's behind this

Solo shop, for now. Built by [@ajitgunturi](https://github.com/ajitgunturi) — R&D engineer, platform & control-plane background, ISB Emerging Leaders alum. If any of this resonates, [let's connect on LinkedIn](https://www.linkedin.com/in/ajitkumargunturi/).

Contributions welcome on the open-source repos (`forge`, `shellcraft`, `drive_sync_flutter`, `arcanelabsio-patterns`). See each project's `CONTRIBUTING.md`.
