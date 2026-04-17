# Arcane Labs — Architecture

Arcane Labs ships **contract-first, ADR-driven, AI-augmented** software. This document names the org-wide engineering patterns that recur across projects so you can see them at a glance without reading five repos.

Individual products in the portfolio have their own architectural choices — some are local-first zero-server (Vael), some are practitioner-mediated SaaS (Longeviti, post-pivot), some are developer CLIs (Forge, Shellcraft). The engineering discipline is shared; the architecture of each product is chosen for its problem.

These are **conventions**, not suggestions. Every active Arcane Labs repo is expected to conform to the engineering patterns below; deviations are documented per-repo as ADRs that reference this file.

---

## The patterns

### 1. Contract-first schemas

**What:** Every component that produces or consumes structured data defines a JSON Schema (or equivalent) for every such artifact. Schemas are the contract; every read is validated; mismatches surface a visible error, never silent corruption.

**Why:** When multiple implementations (skills, apps, forks, future servers) touch the same artifact, the schema is the only source of truth that doesn't drift. Schema-validated I/O makes evolution safe — a new implementation can change the *how* freely as long as the *shape* holds. Every generated file carries a `schema_version` so consumers can reject incompatible versions cleanly.

**Canonical example:** [`longeviti-framework/schemas/`](https://github.com/arcanelabsio/longeviti-framework/tree/main/schemas) — `weekly_plan`, `profile`, `coaching_session`, `rules` schemas, plus [`ALIGNMENT.md`](https://github.com/arcanelabsio/longeviti-framework/blob/main/schemas/ALIGNMENT.md) explaining the producer-consumer contract. Every generated file carries `schema_version`; consumers validate and reject mismatches. Survives the architectural pivot unchanged because it describes shape, not transport. _(Internal to the Arcane Labs org.)_

### 2. Planner / Executor split

**What:** When code interprets inputs and derives actions, the interpretation and the action live in separate modules. The planner reads, thinks, and emits structured directives. The executor takes directives, validates them, and performs side effects. Neither crosses the other's boundary.

**Why:** Debugging an AI-driven system is tractable only if the "what the model decided" and "what the machine did" are two artifacts you can compare. The split is also a safety boundary — executors fail closed on malformed directives from a poisoned planner input.

**Canonical example:** Longeviti's **ELARA → ATLAS** pipeline. ELARA reads reports and tracking data, derives coaching insights, and emits structured directives. ATLAS takes directives only (never raw reports) and writes plans + rules to persistent storage. See [longeviti-framework/README.md](https://github.com/arcanelabsio/longeviti-framework#skills) and [ADR-002](https://github.com/arcanelabsio/longeviti-framework/blob/main/docs/adr/002-atlas-elara-pipeline-separation.md). The separation survives the runtime move from local-skill execution to server-side services unchanged. _(Internal to the Arcane Labs org.)_

### 3. Explicit OAuth scope + path validation at the storage boundary

**What:** Apps that store data in a user's Google Drive choose their OAuth scope explicitly (full `drive`, `drive.file`, or `drive.appdata`) based on architectural need, and validate every path at the I/O boundary — rejecting traversal, absolute paths, empty segments, and escaping query strings. Each scope carries different visibility semantics and different compliance obligations; picking wrong costs years of CASA audits or breaks multi-writer flows silently.

**Why:** OAuth scope is one of the highest-leverage architectural decisions a Drive-backed app makes. Encoding it in the adapter's API (rather than leaving it implicit in which OAuth scopes the auth client requested) makes the decision visible in code review. Path validation is uniform regardless of scope, so injection and traversal are prevented structurally at every call site.

**Canonical example:** [`drive_sync_flutter`](https://github.com/arcanelabsio/drive_sync_flutter) — three named constructors (`userDrive`, `appFiles`, `appData`) map to the three OAuth scopes; `SandboxValidator` enforces path shape uniformly.

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
- Project-level: [`forge/docs/adr/`](https://github.com/arcanelabsio/forge/tree/main/docs/adr), [`vael/docs/adr/`](https://github.com/arcanelabsio/vael/tree/main/docs/adr), [`shellcraft/docs/adr/`](https://github.com/arcanelabsio/shellcraft/tree/main/docs/adr), [`longeviti-framework/docs/adr/`](https://github.com/arcanelabsio/longeviti-framework/tree/main/docs/adr).
- Pattern-level: the [**arcanelabsio-patterns**](https://github.com/arcanelabsio/arcanelabsio-patterns) registry — reusable patterns that project ADRs can reference via `applies_pattern:`. This is the two-tier governance: patterns evolve slowly, decisions freely. _(Registry is internal to the Arcane Labs org.)_

### 6. Live-fetch, read-only execution

**What:** Arcane Labs tools that consume external systems (GitHub, cloud services) do so by running the user's own read-only CLI (`gh`, `git`, etc.) at query time, in the user's environment. No bundled runtime, no proxy, no cache.

**Why:** Zero supply chain beyond what the user already trusts. Answers match what the user would see at the terminal. Debug is one layer.

**Canonical example:** Forge. See [ADR-0002](https://github.com/arcanelabsio/forge/blob/main/docs/adr/0002-direct-gh-execution-no-bundled-runtime.md).

### 7. Privacy boundary as a project-level architectural choice

**What:** Each product picks where the privacy boundary lives — local device, encrypted transport, or server-side with standard compliance — and documents that choice as an ADR. The org does not impose a single privacy posture across the portfolio; it imposes the requirement that the boundary is **chosen deliberately and written down**.

**Why:** A family finance app, a developer CLI, and a practitioner-mediated health platform have fundamentally different threat models. Forcing one privacy architecture across all of them produces bad compromises. The discipline is in the decision being explicit, not in the decision being uniform.

**Canonical example:** **Vael** — finance data encrypted client-side with AES-256-GCM using a family-derived key; Drive is blind ciphertext storage, servers are architecturally absent. See [ADR-0001](https://github.com/arcanelabsio/vael/blob/main/docs/adr/0001-drive-as-blind-encrypted-transport.md). Other products make different choices appropriate to their domain.

### 8. AGENTS.md as the authoritative AI-assistant guide

**What:** Every repo has an `AGENTS.md` that states purpose, key rules, invariants that must not be broken, and where to find the contract. `CLAUDE.md` (if present) is a thin pointer that imports `AGENTS.md` via `@AGENTS.md` so Claude Code auto-loads the same rules. One source of truth across Claude Code, Codex, Copilot, Gemini.

**Why:** Assistants that read project rules shouldn't diverge per tool. AGENTS.md is emerging as the cross-tool convention; pinning on it avoids a per-tool fan-out.

**Canonical examples:** [`forge/AGENTS.md`](https://github.com/arcanelabsio/forge/blob/main/AGENTS.md), [`vael/AGENTS.md`](https://github.com/arcanelabsio/vael/blob/main/AGENTS.md), [`shellcraft/AGENTS.md`](https://github.com/arcanelabsio/shellcraft/blob/main/AGENTS.md), [`drive_sync_flutter`](https://github.com/arcanelabsio/drive_sync_flutter), [`longeviti-framework/AGENTS.md`](https://github.com/arcanelabsio/longeviti-framework/blob/main/AGENTS.md).

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
| Contract-first schemas | Longeviti data framework (JSON schemas for plans, profiles, coaching, rules) |
| Planner / Executor split | Longeviti's ELARA → ATLAS pipeline |
| OAuth scope + path validation at the storage boundary | [drive_sync_flutter](https://github.com/arcanelabsio/drive_sync_flutter) |
| Managed-block installs | [shellcraft](https://github.com/arcanelabsio/shellcraft) (dotfiles), [forge](https://github.com/arcanelabsio/forge) (AI-assistant configs) |
| ADR discipline | All repos; pattern registry at [arcanelabsio-patterns](https://github.com/arcanelabsio/arcanelabsio-patterns) |
| Live-fetch, read-only | [forge](https://github.com/arcanelabsio/forge) |
| Privacy boundary as a project-level choice | [vael](https://github.com/arcanelabsio/vael) (client-side encryption, blind transport) |
| AGENTS.md authoritative | All repos |

---

## Why this document exists

When a first-time visitor lands on the org, the thesis is in the org README but the architectural patterns that operationalize it are scattered across five repos. This page makes the patterns legible without a scavenger hunt. New repos in the org are expected to conform; deviations show up as explicit ADRs, not implicit drift.

If you're proposing an architectural change that affects multiple repos, update this document as part of the change. If this document and a repo disagree, the disagreement is the bug.
