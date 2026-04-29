# OpenSDLC — User Manual

> **Audience**: Engineering managers, tech leads, and individual developers who want OpenSDLC to drive delivery as a real Scrum team — on a brand-new project (Green Field) or an existing codebase (Brown Field).

This manual explains **what to do, in what order, and what to expect** when you run OpenSDLC. It assumes you have already installed OpenSDLC ([README → Quick Start](README.md#-quick-start)).

---

## Table of Contents

- [1. Mental Model](#1-mental-model)
- [2. Before You Start](#2-before-you-start)
- [3. Green Field Workflow (new project)](#3-green-field-workflow-new-project)
- [4. Brown Field Workflow (existing codebase)](#4-brown-field-workflow-existing-codebase)
- [5. Daily Operations](#5-daily-operations)
- [6. Approval Gates — When OpenSDLC Pauses for You](#6-approval-gates--when-opensdlc-pauses-for-you)
- [7. Failure Handling & Tiered Tolerance](#7-failure-handling--tiered-tolerance)
- [8. State Reconciliation (RepoSync)](#8-state-reconciliation-reposync)
- [9. Working with `.sdlc/` Artifacts](#9-working-with-sdlc-artifacts)
- [10. Tips, Gotchas & FAQ](#10-tips-gotchas--faq)

---

## 1. Mental Model

OpenSDLC is a **primary agent** (`OpenSDLC`) that orchestrates a team of specialized **subagents**. Think of it as a Senior Engineering Manager:

- It owns the *process*; you own the *priorities and approvals*.
- Every code change is traceable to a `TSK-XXXX` or `BUG-XXXX` markdown ticket under `.sdlc/`.
- Nothing is committed to a sprint, and nothing is shipped, without your sign-off.
- The `.sdlc/` directory is the source of truth and is checked into Git like any other code.

Two operating modes are auto-detected on first contact:

| Mode | Trigger | Extra stage |
|------|---------|-------------|
| **Green Field** | empty repo / no source / no `.sdlc/` | **Bootstrap** scaffolds standards, ADR-0001, CI, migrations |
| **Brown Field** | existing codebase + Git history | **LegacyOnboarding** runs `LegacyScout` + `RepoSync` to map the code |

---

## 2. Before You Start

### Prerequisites

- [OpenCode CLI](https://opencode.ai/docs) installed.
- A Git repository (init'd locally is fine — `git init` if you haven't).
- A model configured in OpenCode (Claude, GPT, Gemini, or local).
- A clear, written goal for what you want to build or change.

### One-time setup

```bash
# from your project root
opencode --agent OpenSDLC
```

On first run, OpenSDLC will:
1. Detect the mode (Green Field vs Brown Field) and ask you to confirm.
2. Run **Bootstrap** or **LegacyOnboarding**.
3. Stop and ask for approval before going further.

> **Tip**: Always launch OpenSDLC from the *root* of the repository so it can see `.git/`, source manifests, and the future `.sdlc/`.

---

## 3. Green Field Workflow (new project)

Use this when you are starting from an empty (or near-empty) repository.

### Step 1 — Describe the product

Tell OpenSDLC what you want, with enough detail to seed an Epic. Example:

```text
Build a multi-tenant SaaS for invoicing: customers can self-register, create invoices,
send them via email, and accept payment via Stripe. Stack: TypeScript, Node, Postgres,
Next.js. Target deploy: Vercel + Neon.
```

### Step 2 — Approve the bootstrap

OpenSDLC will:
1. Detect Green Field and ask you to confirm.
2. Scaffold the directory structure:
   ```
   .sdlc/{product,epics,stories,sprints,tasks,defects,debt,adrs,impact,reconcile,releases,retros}
   .opencode/context/core/standards/{code-quality,tests,docs}.md   ← starter standards
   ```
3. Delegate to `ArchitectureAnalyzer` + `ADRManager` to write **`ADR-0001 — System Architecture Baseline`**.
4. Delegate to `DatabaseManager` to scaffold migrations (e.g., Prisma or Flyway).
5. Delegate to `DevOpsAgent` to scaffold a **Sprint-1 CI workflow** (e.g., `.github/workflows/ci.yml`).

**Your job**: read `ADR-0001`, the seeded standards, and the CI YAML. Edit them if needed, then approve.

### Step 3 — Requirement Elicitation (PO asks questions)

Before creating any stories, `ProductOwner` will **ask you at least 5 clarifying questions** about:
- **Who** — target users / personas.
- **What (scope)** — what's in, what's explicitly out.
- **Why (value)** — business driver, KPI impact.
- **How (constraints)** — tech stack, 3rd-party integrations, regulatory, data sensitivity.
- **Non-functional** — performance, security, accessibility, i18n.
- **Edge cases** — failure modes, concurrent users, data limits.
- **Definition of success** — how you'll verify it works.

Answer these questions. PO then summarises and asks you to confirm the refined understanding. Only after your confirmation does story creation begin.

> **Why?** A one-liner like "add SSO" is dangerously ambiguous. SAML or OIDC? Which IdPs? Self-serve provisioning? MFA enforcement? Elicitation prevents building the wrong thing.

### Step 4 — Product Discovery

`StoryMapper` and `ProductOwner` produce `EPIC-XXXX` + `STY-XXXX` files with acceptance criteria. `PrioritizationEngine` ranks them.

**Your job**: review the Epic shape and the ranked story list. Approve the proposed MVP slice.

### Step 5 — Sprint Planning

`ScrumMaster` proposes Sprint 1 sized to capacity. (Green Field has no historical velocity, so a conservative default is used.)

**Your job**: approve the sprint commitment.

### Step 6 — Execution

OpenSDLC executes one story at a time. For each story:
- `CoderAgent` implements the tasks.
- `DatabaseManager` writes any migrations (forward + rollback).
- `BuildAgent` checks lint/types/build locally.
- `TestEngineer` writes and runs tests.
- `CodeReviewer` does an independent review.
- `DevOpsAgent` confirms CI is green on the branch.
- `DocWriter` updates docs.
- `ProductOwner` accepts against the criteria.

### Step 7 — Review, Retro, Release

After the sprint:
- **Review**: `RepoSync` reconciles `.sdlc/` ↔ Git, then `ScrumMaster` reports velocity and `ProductOwner` accepts.
- **Retro**: Start / Stop / Continue → action items become tasks for next sprint.
- **Release** (when ready): `ReleaseManager` cuts `REL-{semver}.md`, `DatabaseManager` and `DevOpsAgent` verify migrations and pipelines, you approve, deploy.

---

## 4. Brown Field Workflow (existing codebase)

Use this when you have an existing repository with code, Git history, and (probably) outdated docs.

### Step 1 — Launch and confirm Brown Field

```bash
cd your-existing-repo
opencode --agent OpenSDLC
> "We need to add SSO via SAML to the customer portal."
```

OpenSDLC detects existing code and proposes Brown Field. **Confirm.**

### Step 2 — Legacy Onboarding (read-only)

This is the most important step. OpenSDLC will:
1. Run `RepoSync` to capture Git state and reconcile any pre-existing `.sdlc/`.
2. Run `LegacyScout` to produce **`.sdlc/product/legacy-map.md`** with:
   - Stack inventory (frameworks, versions).
   - Module/package boundaries and inter-module imports.
   - Reusable shared components and utilities.
   - Test infrastructure and current coverage baseline.
   - Existing CI pipelines.
   - Hotspots (high-churn files) and dead zones.
3. `ContextScout` loads existing standards (or recommends extracting standards from dominant patterns rather than imposing new ones).

**Your job**: read `legacy-map.md` and validate it. Correct any wrong assumptions before stories are written. **No code is changed in this step.**

### Step 3 — Requirement Elicitation + Story Drafting (with Impact Analysis)

Just like Green Field, `ProductOwner` first asks you **at least 5 clarifying questions** about scope, personas, constraints, NFRs, and edge cases. After you confirm the refined summary, stories are created.

**For brown-field, every candidate story must also have an Impact Analysis** before it can be committed to a sprint. `LegacyScout` produces `.sdlc/impact/IMP-{STY-ID}.md` with:

- **Blast radius** — files/modules likely modified, with risk per path.
- **Reusable assets** — concrete pointers (`@/shared/Money`, `BaseRepository`) so the team doesn't reinvent.
- **Downstream consumers** — who imports the modules being changed.
- **Database / schema impact** — tables, columns, queries touched.
- **Required regression test scope** — what `TestEngineer` must run before DoD.
- **Recommendation** — Proceed / Re-scope / Block on prerequisite TSK.

**Your job**: read each Impact Analysis. If the blast radius is bigger than expected, decide whether to re-scope.

### Step 4 — Sprint Planning, Execution, Review

Same as Green Field, with two brown-field-specific differences:

- **Coverage gate is delta-based**: new and modified code must hit ≥ 80% coverage; whole-project coverage is tracked but not gated. This makes legacy rescues feasible.
- **Tech-debt quarantine is enforced**: if a subagent spots an unrelated code smell while implementing a story, it files a `DEBT-XXXX` ticket and continues — it does NOT silently fix it. You decide later whether to schedule the debt.

### Step 5 — Reconcile Frequently

If human teammates push code or merge PRs while OpenSDLC is running, `.sdlc/` will drift. Re-run `RepoSync` (or just start a new planning session — it runs automatically pre-planning and pre-review). Drift report lands at `.sdlc/reconcile/sync-{YYYY-MM-DD}.md`.

---

## 5. Daily Operations

### Daily Standup

Ask OpenSDLC:

```text
Run a daily standup for the active sprint.
```

`ScrumMaster` appends a standup section to the active sprint file: completed since last standup, in-progress, blockers, burn-down delta.

### Adding a story mid-sprint

Don't (Scrum 101). If urgent:

```text
We have an urgent need: <describe>. Please file it as a hotfix BUG or scope it for the next sprint.
```

OpenSDLC files a `BUG-XXXX` if it's a defect, or recommends carry-over.

### Filing a defect manually

```text
File a BUG: <steps to reproduce, expected, actual>.
```

`ScrumMaster` creates `BUG-XXXX` with severity, links to the affected `STY` / `TSK`, and triages with the PO.

### Filing technical debt

```text
File DEBT: the legacy `LegacyPaymentService` duplicates currency math from Money util.
```

`DEBT-XXXX` is added to `.sdlc/debt/`, visible to the next planning session.

---

## 6. Approval Gates — When OpenSDLC Pauses for You

OpenSDLC pauses and asks for explicit approval at these moments:

| Gate | Stage | What you're approving |
|------|-------|------------------------|
| Mode confirmation | Stage -1 | Green Field vs Brown Field classification |
| Bootstrap sign-off | Stage 0a | ADR-0001, seeded standards, CI scaffold |
| Elicitation answers | Stage 1 | Answer PO's 5+ clarifying questions, confirm the Requirement Summary |
| Epic + story shape | Stage 1 | The proposed Epic and ranked stories |
| Sprint commitment | Stage 2 | The sprint goal and committed stories |
| Tier-2 failure triage | Stage 4/6 | A new `BUG-XXXX` and proposed fix plan |
| Release sign-off | Stage 9 | Release notes, migration plan, rollback plan |

Routine reads, searches, and ContextScout discovery do **not** require approval.

---

## 7. Failure Handling & Tiered Tolerance

OpenSDLC does **not** ask you to approve every linting error. Failures are tiered:

### Tier 1 — Auto-fix (max 2 attempts, logged)
Lint, formatting, trivial syntax, mechanical type fixes, lockfile drift. Each attempt is recorded in the TSK Execution Log. After 2 failed attempts, escalate to Tier 2.

### Tier 2 — File a `BUG-XXXX`, request approval
Failing tests, persistent build errors, ERROR-severity lint rules (e.g., SQL injection), flaky tests, architectural violations. **Never auto-fixed.** You see the RCA and approve the fix plan.

### Tier 3 — Halt the sprint
Two or more Tier-2 failures on the same story, or any failure touching `.env`, secrets, prod configs, or migration rollback paths. Sprint is paused; PM is paged immediately.

This means OpenSDLC stays out of your way for trivial fixes but stops cold on real defects. You can change the tier policy by editing the `stop_on_failure` rule in `.opencode/agent/core/opensdlc.md`.

---

## 8. State Reconciliation (RepoSync)

The `.sdlc/` directory and the Git repository must agree. `RepoSync` is invoked automatically:

- **Before sprint planning** — make sure planning is based on reality.
- **Before sprint review** — make sure "Done" claims match merged code.
- **On demand** — when you suspect drift.

`RepoSync` detects:

| Drift Type | Meaning | Default Resolution |
|-----------|---------|--------------------|
| Ghost Done | STY/TSK is Done but no commit references it on `main` | Reopen story, request explanation |
| Stealth Merge | Commit on `main` with no `TSK-XXXX` link | File retroactive ticket OR recommend revert |
| Stale In-Progress | TSK in-progress for too long, no commits | Flag to ScrumMaster |
| Tag Drift | Latest Git tag not in any `REL-{semver}.md` | Escalate to ReleaseManager |
| Local Dirt | Uncommitted changes at sprint boundary | Flag (do not stash) |

Reports land at `.sdlc/reconcile/sync-{YYYY-MM-DD}.md`.

---

## 9. Working with `.sdlc/` Artifacts

| Path | Purpose |
|------|---------|
| `.sdlc/product/vision.md` | Product vision & North Star |
| `.sdlc/product/roadmap.md` | Quarterly / release roadmap |
| `.sdlc/product/backlog.md` | Ordered product backlog |
| `.sdlc/product/legacy-map.md` | Brown-field codebase map (LegacyScout) |
| `.sdlc/product/mode.md` | Detected mode (Green / Brown) + rationale |
| `.sdlc/epics/EPIC-XXXX-*.md` | Epic-level objectives |
| `.sdlc/stories/STY-XXXX-*.md` | User stories with DoR/DoD |
| `.sdlc/tasks/TSK-XXXX-*.md` | Atomic engineering tasks |
| `.sdlc/defects/BUG-XXXX-*.md` | Defects with RCA + fix plan |
| `.sdlc/debt/DEBT-XXXX-*.md` | Technical debt / refactor candidates |
| `.sdlc/adrs/ADR-XXXX-*.md` | Architecture Decision Records |
| `.sdlc/impact/IMP-{STY-ID}.md` | Brown-field per-story Impact Analysis |
| `.sdlc/reconcile/sync-*.md` | RepoSync drift reports |
| `.sdlc/sprints/sprint-NN.md` | Sprint goal, commitments, ceremonies, burn-down |
| `.sdlc/releases/REL-*.md` | Release notes, deployment plan, sign-offs |
| `.sdlc/retros/retro-NN.md` | Retrospective (Start / Stop / Continue) |

**Editing tickets directly** is allowed and encouraged — the `.sdlc/` is just markdown. If you change a status by hand, OpenSDLC's audit trail expects an Execution Log entry. `RepoSync` will flag inconsistencies on the next reconcile.

---

## 10. Tips, Gotchas & FAQ

**Q: Can I skip the approval gates?**
No. They are the contract. You can adjust *which* gates exist by editing the `enterprise_sdlc_rules` block in `.opencode/agent/core/opensdlc.md`, but disabling them defeats the purpose.

**Q: My team is small / solo. Is this overkill?**
Probably yes for a one-day prototype. Use OpenCoder or OpenAgent for that. Use OpenSDLC when you need traceability, when you'll demo to a stakeholder, or when the codebase will outlive the prototype.

**Q: My existing codebase has 20% coverage. OpenSDLC won't fail me?**
Correct. Coverage is **delta-based**: only the new/modified code must hit ≥ 80%. Whole-project coverage is a *trend* metric, not a gate.

**Q: A subagent saw a bug I didn't ask about. Why didn't it fix it?**
That's the `tech_debt_quarantine` rule. Out-of-scope smells become `DEBT-XXXX` tickets so you control side-effects. You can schedule the debt in a future sprint or wontfix it.

**Q: A human pushed a commit while OpenSDLC was thinking. What happens?**
Next reconcile, `RepoSync` detects the merge. If it has a `TSK-XXXX` reference in the commit message, it's automatically linked. If not, it's flagged as a Stealth Merge for triage.

**Q: How do I customize standards (Java naming, Go style, etc.)?**
Edit `.opencode/context/core/standards/code-quality.md` (and friends). OpenSDLC's `ContextScout` will load them on every invocation. For language-specific rules, add files like `code-quality-java.md` and reference them.

**Q: How do I switch models?**
Use OpenCode's normal model configuration. OpenSDLC works with Claude, GPT, Gemini, or local models. Subagent temperature is preset to 0.1 for determinism — don't raise it without a reason.

**Q: Where do I file feedback on the OpenSDLC agent itself?**
Open an issue on the [OpenAgentsSDLC repo](https://github.com/hyvuonggia/OpenAgentsSDLC).

---

## Appendix — One-page Cheat Sheet

```
GREEN FIELD                          BROWN FIELD
───────────                          ───────────
1. opencode --agent OpenSDLC         1. opencode --agent OpenSDLC
2. Confirm: Green Field              2. Confirm: Brown Field
3. Approve: ADR-0001 + standards     3. Read: legacy-map.md
   + Sprint-1 CI                        (no code changes yet)
4. Answer PO's 5+ questions          4. Answer PO's 5+ questions
   Confirm: Requirement Summary         Confirm: Requirement Summary
5. Approve: Epic + stories           5. Approve: Epic + stories
6. Approve: Sprint commitment        6. Read each IMP-{STY}.md
7. Watch execution                      Approve: Sprint commitment
   - Tier-1 fixes auto              7. Watch execution
   - Tier-2 → you triage BUG             - Delta coverage ≥ 80%
   - Tier-3 → sprint halt                - Smells → DEBT (not silent fix)
8. Sprint Review (RepoSync)          8. Sprint Review (RepoSync)
9. Retro                             9. Retro
10. Release (when ready)             10. Release (when ready)
```

---

**Next:** see [README.md](README.md) for installation, [docs/](docs/) for deep dives, and [.opencode/agent/core/opensdlc.md](.opencode/agent/core/opensdlc.md) for the agent definition itself.
