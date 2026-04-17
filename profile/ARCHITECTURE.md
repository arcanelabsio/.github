# Arcane Labs — Architecture

Arcane Labs ships **local-first, bring-your-own-AI, contract-first** software. No servers. User's data lives on their own Drive. User's AI subscription does the compute. This document names the org-wide patterns that operationalize that posture so you can see them at a glance without reading five repos.

These are **conventions**, not suggestions. Every active Arcane Labs repo is expected to conform; deviations are documented per-repo as ADRs that reference this file.

---

## The patterns

### 1. Contract-first schemas

**What:** Every app that writes artifacts to Drive or exchanges data with another component defines a JSON Schema for every such file. Schemas are the contract; every read is validated; mismatches surface a visible error, never silent corruption.

**Why:** When multiple implementations (skills, apps, forks) touch the same file, the schema is the only source of truth that doesn't drift. Schema-validated I/O makes forking safe — a fork can change the *how* freely as long as the *shape* holds.

**Canonical example:** [`longeviti-framework/schemas/`](https://github.com/arcanelabsio/longeviti-framework/tree/main/schemas) — `weekly_plan`, `profile`, `coaching_session`, `rules` schemas, plus [`ALIGNMENT.md`](https://github.com/arcanelabsio/longeviti-framework/blob/main/schemas/ALIGNMENT.md) explaining the app-consumer contract. Every generated file carries `schema_version`; the app defaults and validates.

### 2. Planner / Executor split

**What:** When code interprets inputs and derives actions, the interpretation and the action live in separate modules. The planner reads, thinks, and emits structured directives. The executor takes directives, validates them, and performs side effects. Neither crosses the other's boundary.

**Why:** Debugging an AI-driven system is tractable only if the "what the model decided" and "what the machine did" are two artifacts you can compare. The split is also a safety boundary — executors fail closed on malformed directives from a poisoned planner input.

**Canonical example:** Longeviti's **ELARA → ATLAS** pipeline. ELARA reads reports and tracking data, derives coaching insights, and emits structured directives. ATLAS takes directives only (never raw reports) and writes plans + rules to Drive. See [longeviti-framework/README.md](https://github.com/arcanelabsio/longeviti-framework#skills) and [ADR-002](https://github.com/arcanelabsio/longeviti-framework/blob/main/docs/adr/002-atlas-elara-pipeline-separation.md).

### 3. Sandboxed `.app/<appName>/` paths

**What:** Arcane Labs apps that store data in a user's Google Drive do so under `{DRIVE_BASE}/.app/<appName>/`. Path construction is anchored at this root; no app reads or writes outside its sandbox. The spec lives at [`longeviti-framework/docs/arcane-labs-drive-sandbox-spec.md`](https://github.com/arcanelabsio/longeviti-framework/blob/main/docs/arcane-labs-drive-sandbox-spec.md).

**Why:** Multiple Arcane Labs apps can coexist on the same Drive. Uninstalling one doesn't touch another. Third parties can claim "Arcane-Labs-compatible Drive layout" and plug in. Path traversal is structurally prevented at the I/O boundary, not defended after the fact.

**Canonical example:** `drive_sync_flutter` (enforces in code via `SandboxedDrivePath`), consumed by Longeviti.

### 4. Managed-block installs

**What:** Any file Arcane Labs tooling writes into a user's environment (dotfiles, AI-assistant config, plugin definitions) is inserted as a single **managed block** delimited by named markers. Re-runs replace only what's inside the block. Everything outside is the user's and must survive every run.

**Why:** Developers don't run tools that stomp their customizations. Managed-block adoption makes tools safe to re-run on existing machines without asking the user to stage backups — the edit surface is explicit and localized.

**Canonical examples:**
- **Shellcraft** — `~/.zprofile`, `~/.zshrc`, `~/.gitconfig`, `~/.tmux.conf` adopted via include blocks pointing at `~/.config/shellcraft/`. See [DESIGN.md](https://github.com/arcanelabsio/shellcraft/blob/main/DESIGN.md) and [ADR-0002](https://github.com/arcanelabsio/shellcraft/blob/main/docs/adr/0002-managed-block-dotfile-adoption.md).
- **Forge** — installed prompt files in each assistant's config directory use named markers. See [ADR-0003](https://github.com/arcanelabsio/forge/blob/main/docs/adr/0003-managed-block-markers-preserve-user-edits.md).

### 5. ADR discipline

**What:** Every code repo has `docs/adr/` with numbered ADRs. Decisions are written down at the time they're made; ADRs are immutable after acceptance (supersede rather than edit). Where an org-level pattern applies, the ADR's frontmatter includes `applies_pattern: PTRN-NNN` pointing at the registry.

**Why:** The tooling is the wrong place to encode rationale. ADRs are the archaeology layer that keeps the codebase legible years later.

**Canonical examples:**
- Project-level: [`longeviti-framework/docs/adr/`](https://github.com/arcanelabsio/longeviti-framework/tree/main/docs/adr), [`forge/docs/adr/`](https://github.com/arcanelabsio/forge/tree/main/docs/adr), [`vael/docs/adr/`](https://github.com/arcanelabsio/vael/tree/main/docs/adr), [`shellcraft/docs/adr/`](https://github.com/arcanelabsio/shellcraft/tree/main/docs/adr).
- Pattern-level: the [**arcanelabsio-patterns**](https://github.com/arcanelabsio/arcanelabsio-patterns) registry — reusable patterns that project ADRs can reference via `applies_pattern:`. This is the two-tier governance: patterns evolve slowly, decisions freely.

### 6. Live-fetch, read-only execution

**What:** Arcane Labs tools that consume external systems (GitHub, cloud services) do so by running the user's own read-only CLI (`gh`, `git`, etc.) at query time, in the user's environment. No bundled runtime, no proxy, no cache.

**Why:** Zero supply chain beyond what the user already trusts. Answers match what the user would see at the terminal. Debug is one layer.

**Canonical example:** Forge. See [ADR-0002](https://github.com/arcanelabsio/forge/blob/main/docs/adr/0002-direct-gh-execution-no-bundled-runtime.md).

### 7. Privacy by architecture, not by convention

**What:** When an app handles sensitive data, that data physically does not exist in the repo tree — it lives in user-owned cloud storage or a local encrypted store. Accidental commits are structurally impossible, not just against policy.

**Why:** Discipline is fragile; architecture is durable. A publicly forkable repo that physically cannot contain personal data removes a whole class of leak.

**Canonical examples:**
- **Longeviti** — health data lives on user's Drive, never in the repo. See [ADR-001](https://github.com/arcanelabsio/longeviti-framework/blob/main/docs/adr/001-google-drive-as-data-layer.md).
- **Vael** — finance data encrypted client-side with AES-256-GCM using a family-derived key; Drive is blind ciphertext storage. See [ADR-0001](https://github.com/arcanelabsio/vael/blob/main/docs/adr/0001-drive-as-blind-encrypted-transport.md).

### 8. AGENTS.md as the authoritative AI-assistant guide

**What:** Every repo has an `AGENTS.md` that states purpose, key rules, invariants that must not be broken, and where to find the contract. `CLAUDE.md` (if present) is a thin pointer that imports `AGENTS.md` via `@AGENTS.md` so Claude Code auto-loads the same rules. One source of truth across Claude Code, Codex, Copilot, Gemini.

**Why:** Assistants that read project rules shouldn't diverge per tool. AGENTS.md is emerging as the cross-tool convention; pinning on it avoids a per-tool fan-out.

**Canonical examples:** [`forge/AGENTS.md`](https://github.com/arcanelabsio/forge/blob/main/AGENTS.md), [`vael/AGENTS.md`](https://github.com/arcanelabsio/vael/blob/main/AGENTS.md), [`shellcraft/AGENTS.md`](https://github.com/arcanelabsio/shellcraft/blob/main/AGENTS.md), [`longeviti-framework/AGENTS.md`](https://github.com/arcanelabsio/longeviti-framework/blob/main/AGENTS.md).

---

## The two-tier governance

Arcane Labs separates **patterns** from **decisions**:

- **Patterns** live in [`arcanelabsio-patterns`](https://github.com/arcanelabsio/arcanelabsio-patterns). They're the reusable solution shapes (e.g., "offline-first sync topology," "retry with exponential backoff"). Patterns evolve slowly — they change only when we learn something new about the problem shape.
- **Decisions** live in each project's `docs/adr/`. A project adopting a pattern sets `applies_pattern: PTRN-NNN` in the ADR frontmatter; tuning the parameters (3 retries vs 5, which timeout, which merge strategy) is a project decision.

This separation is deliberate: projects stay free to diverge from a pattern; divergences are recorded as a fact in that project's own ADR without requiring a change to the pattern itself.

---

## Where each pattern is most faithfully implemented

| Pattern | Canonical implementation |
|---|---|
| Contract-first schemas | [longeviti-framework](https://github.com/arcanelabsio/longeviti-framework) |
| Planner / Executor split | [longeviti-framework](https://github.com/arcanelabsio/longeviti-framework) (ELARA → ATLAS) |
| Sandboxed Drive paths | `drive_sync_flutter` + [longeviti-framework spec](https://github.com/arcanelabsio/longeviti-framework/blob/main/docs/arcane-labs-drive-sandbox-spec.md) |
| Managed-block installs | [shellcraft](https://github.com/arcanelabsio/shellcraft) (dotfiles), [forge](https://github.com/arcanelabsio/forge) (AI-assistant configs) |
| ADR discipline | All repos; pattern registry at [arcanelabsio-patterns](https://github.com/arcanelabsio/arcanelabsio-patterns) |
| Live-fetch, read-only | [forge](https://github.com/arcanelabsio/forge) |
| Privacy by architecture | [longeviti-framework](https://github.com/arcanelabsio/longeviti-framework) (Drive layer), [vael](https://github.com/arcanelabsio/vael) (client-side encryption) |
| AGENTS.md authoritative | All repos |

---

## Why this document exists

When a first-time visitor lands on the org, the thesis is in the org README but the architectural patterns that operationalize it are scattered across five repos. This page makes the patterns legible without a scavenger hunt. New repos in the org are expected to conform; deviations show up as explicit ADRs, not implicit drift.

If you're proposing an architectural change that affects multiple repos, update this document as part of the change. If this document and a repo disagree, the disagreement is the bug.
