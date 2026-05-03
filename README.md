<div align="center">

# OpenSDLC

### Your AI-Powered Agile Scrum Team

**An enterprise SDLC orchestrator that runs a complete Scrum team of AI subagents — with markdown-based ticketing, sprint cadence, Definition of Ready / Definition of Done enforcement, and human approval at every commitment point.**

🏢 **Enterprise SDLC** — Full Scrum lifecycle from backlog to release  
👥 **20 Specialized Subagents** — BA, PO, SM, Release Manager, Devs, QA, and more  
📋 **Markdown Ticketing** — Requirements, epics, stories, tasks, defects, sprints, ADRs, releases under `.sdlc/`  
✋ **Approval Gates** — Human sign-off at every commitment point  
🔁 **DoR / DoD Enforcement** — Nothing enters a sprint unready; nothing ships incomplete  
🔍 **Full Traceability** — Every code change traceable to a ticket with timestamped audit logs

**Multi-language:** TypeScript • Python • Go • Rust • C# • Any language  
**Model Agnostic:** Claude • GPT • Gemini • Local models

[![License: MIT](https://img.shields.io/badge/License-MIT-3fb950?style=flat-square&labelColor=black)](https://opensource.org/licenses/MIT)

</div>

---

> **Built on [OpenCode](https://opencode.ai)** — An open-source AI coding framework. OpenSDLC extends it with a full Agile/Scrum team, enterprise process controls, and markdown-based project management.

---

## What Is OpenSDLC?

OpenSDLC is an AI orchestrator agent that operates like a **Senior Engineering Manager running a real Scrum team**. Instead of just generating code, it manages the entire software delivery lifecycle:

1. **Product Discovery** — breaks your request into epics and user stories
2. **Sprint Planning** — commits a realistic sprint sized to capacity
3. **Execution** — implements stories one at a time with build/test/review gates
4. **Validation** — enforces Definition of Done before marking anything complete
5. **Release** — manages semver, changelog, sign-offs, and deployment readiness

Everything is tracked in **plain markdown files** under `.sdlc/` — your audit trail, your single source of truth, fully version-controlled.

---

## 🚀 Quick Start

**Prerequisites:** [OpenCode CLI](https://opencode.ai/docs) (free, open-source) • Bash 3.2+ • Git

### Install

**Local (project-specific):**

```bash
curl -fsSL https://raw.githubusercontent.com/hyvuonggia/OpenAgentsSDLC/main/install.sh | bash -s developer
```

**Global (user-wide, available across all projects):**

```bash
curl -fsSL https://raw.githubusercontent.com/hyvuonggia/OpenAgentsSDLC/main/install.sh | bash -s developer --install-dir ~/.config/opencode
```

> The `developer` profile includes **OpenAgent** (general), **OpenCoder** (production dev), and **OpenSDLC** (enterprise Scrum) plus all 19 subagents.

### Update

The installer skips files that already exist. To pull the **latest** version of every installed agent, subagent, command, skill, and context, use the updater:

**Auto-detect (project-local first, then global):**

```bash
curl -fsSL https://raw.githubusercontent.com/hyvuonggia/OpenAgentsSDLC/main/update.sh | bash
```

**Update a project-local install:**

```bash
cd /path/to/your/project
curl -fsSL https://raw.githubusercontent.com/hyvuonggia/OpenAgentsSDLC/main/update.sh | bash
```

**Update a global install:**

```bash
curl -fsSL https://raw.githubusercontent.com/hyvuonggia/OpenAgentsSDLC/main/update.sh | bash -s -- --install-dir ~/.config/opencode
```

The updater overwrites every installed `.md`, `.ts`, `.sh`, and `.json` component in place with the latest version from the `main` branch (set `OPENCODE_BRANCH` to pull from another branch). Files that no longer exist on the remote are left untouched. Each file is backed up before replacement and restored automatically if the download fails.

### Start Using OpenSDLC

```bash
opencode --agent OpenSDLC
> "Build an invoicing system with multi-tenant support"
```

**What happens:**
1. **Requirement Intake** — BusinessAnalyst writes `.sdlc/requirements/draft-REQ-0001-{slug}.md` with clarifying questions. **You answer the questions inline in that file**, then rename it to `REQ-0001-{slug}.md` (drop the `draft-` prefix). BA validates and produces the Requirement Summary.
2. **Discovery** — ContextScout loads your project standards
3. **Product Discovery** — StoryMapper + ProductOwner create epics and stories from the finalised `REQ-XXXX.md` (no re-eliciting in chat)
4. **Sprint Planning** — ScrumMaster sizes the sprint to capacity, filters by DoR
5. **You approve** the sprint commitment
6. **Execution** — CoderAgent implements, TestEngineer tests, TechnicalLead reviews (separation of duties)
7. **Validation** — ProductOwner accepts against the written criteria
8. **Release** — ReleaseManager cuts the release with sign-offs and changelog

Every step is traceable. Every transition is logged. You approve before anything is committed.

> 📖 **New here?** Read the **[OpenSDLC User Manual](MANUAL.md)** — a step-by-step guide for both **Green Field** (new project) and **Brown Field** (existing codebase) workflows, including approval gates, tiered failure handling, state reconciliation, and a one-page cheat sheet.

---

## 👥 The Scrum Team — 20 Subagents

OpenSDLC orchestrates 20 specialized subagents organized into four groups:

### Scrum Roles

| Subagent | Role | What It Does |
|----------|------|--------------|
| **BusinessAnalyst** | First-touch on every new request | Drafts `.sdlc/requirements/draft-REQ-XXXX-{slug}.md` with clarifying questions for the human to answer in-file; validates the renamed `REQ-XXXX-{slug}.md` before any planning runs |
| **ProductOwner** | Owns *what* to build | Maintains ranked backlog, refines stories from the finalised `REQ-XXXX.md`, writes Given/When/Then acceptance criteria, enforces DoR, accepts/rejects completed work |
| **ScrumMaster** | Owns *how we work* | Runs ceremonies (planning, standup, review, retro), tracks velocity and burn-down, enforces DoR/DoD, surfaces blockers |
| **ReleaseManager** | Owns *shipping* | Plans releases, manages semver + CHANGELOG.md, collects 4-way sign-offs (Engineering/QA/Security/Product), validates post-deploy |

### Planning & Architecture

| Subagent | What It Does |
|----------|--------------|
| **StoryMapper** | Maps user needs → personas → epics → stories → vertical slices |
| **ArchitectureAnalyzer** | DDD-driven bounded context identification, module boundaries, domain relationships |
| **PrioritizationEngine** | RICE / WSJF scoring, MVP vs post-MVP slicing |
| **ADRManager** | Records Architecture Decision Records for significant technical choices |
| **ContractManager** | Defines and validates API/service contracts between modules |
| **TaskManager** | Breaks committed stories into atomic, dependency-aware engineering tasks |

### Discovery

| Subagent | What It Does |
|----------|--------------|
| **ContextScout** | Discovers project context files (coding standards, patterns) BEFORE coding |
| **ExternalScout** | Fetches live, version-specific docs for external libraries |

### Development & Quality

| Subagent | What It Does |
|----------|--------------|
| **CoderAgent** | Implements individual coding tasks |
| **BatchExecutor** | Runs parallel batches of tasks within a sprint |
| **TestEngineer** | Authors unit/integration/acceptance tests (TDD-aligned) |
| **TechnicalLead** | Independent security + quality review (never reviews its own code) |
| **BuildAgent** | Type-checks, lints, validates the build |
| **DocWriter** | Updates documentation, READMEs, runbooks |
| **FrontendSpecialist** | React, Vue, modern CSS architecture |
| **DevOpsSpecialist** | CI/CD, infrastructure as code, deployment automation |

---

## 📋 The `.sdlc/` Tracking System

OpenSDLC maintains all project state as markdown files under `.sdlc/`. Commit them to git — they are your audit trail.

```
.sdlc/
├── product/
│   ├── vision.md              # Product vision & North Star metric
│   ├── roadmap.md             # Quarterly / release roadmap
│   └── backlog.md             # Ordered product backlog
├── requirements/
│   ├── draft-REQ-XXXX-{slug}.md   # BA-drafted questionnaire awaiting human answers
│   └── REQ-XXXX-{slug}.md         # Human-finalised requirement (single source of truth)
├── epics/
│   └── EPIC-XXXX-{slug}.md   # Epic-level objectives
├── stories/
│   └── STY-XXXX-{slug}.md    # User stories (DoR + DoD checklists)
├── sprints/
│   └── sprint-{NN}.md        # Sprint goal, capacity, burn-down, ceremonies log
├── tasks/
│   └── TSK-XXXX-{slug}.md    # Atomic engineering tasks
├── defects/
│   └── BUG-XXXX-{slug}.md    # Defects with RCA + fix plan
├── adrs/
│   └── ADR-XXXX-{slug}.md    # Architecture Decision Records
├── releases/
│   └── REL-{semver}.md       # Release plan, sign-offs, post-release status
└── retros/
    └── retro-{NN}.md         # Sprint retrospectives (Start/Stop/Continue)
```

Every file includes an **Execution Log** — a timestamped append-only log of every state transition. This is the audit trail.

---

## 🔄 The Full Scrum Workflow

```
                    ┌─────────────────────────────────────────────┐
                    │            You (PM / Human)                  │
                    │   Approve at every commitment point          │
                    └──────────────┬──────────────────────────────┘
                                   │
       ┌────────────┬──────────────┬──────────────┬──────────────┐
       ▼            ▼              ▼              ▼              ▼
  ┌──────────┐ ┌─────────┐  ┌──────────────┐  ┌──────────────┐
  │Requirement│ │Discovery│  │   Product    │  │   Sprint     │
  │  Intake   │ │         │  │  Discovery   │  │  Planning    │
  │           │ │Context  │  │              │  │              │
  │BusinessAna│ │Scout    │  │StoryMapper   │  │ScrumMaster   │
  │ draft-REQ │ │External │  │Arch.Analyzer │  │ProductOwner  │
  │ → REQ.md  │ │Scout    │  │Prioritizer   │  │capacity+DoR  │
  │ (human    │ └─────────┘  │ProductOwner  │  └──────┬───────┘
  │ renames)  │              │(reads REQ)   │         │
  └─────┬─────┘              └──────────────┘         │
        ▼ HUMAN ANSWERS IN FILE                       │
                                              ▼ APPROVAL GATE
                                                              │
       ┌──────────────────────────────────────────────────────┘
       ▼
  ┌──────────────────────────────────────────────────────────────┐
  │                     Sprint Execution                          │
  │                                                                │
  │  For each story (priority order):                              │
     │    TaskManager → CoderAgent → BuildAgent → TestEngineer       │
     │    → TechnicalLead → DocWriter → ADRManager → ProductOwner     │
  │                                                                │
  │  Separation of duties: coder ≠ reviewer                       │
  │  Stop-on-failure: BUG-XXXX auto-created on test/build fail    │
  └─────────────────────────────┬────────────────────────────────┘
                                │
       ┌────────────────────────┼────────────────────────┐
       ▼                        ▼                        ▼
  ┌──────────┐          ┌──────────────┐         ┌──────────────┐
  │  Sprint  │          │    Sprint    │         │    Release   │
  │  Review  │          │    Retro     │         │   Planning   │
  │          │          │              │         │              │
  │velocity  │          │Start/Stop/   │         │ReleaseManager│
  │done vs   │          │Continue      │         │semver+sign-  │
  │committed │          │action items  │         │offs+deploy   │
  └──────────┘          └──────────────┘         └──────────────┘
                                                    ▼ APPROVAL GATE
```

### Stage-by-Stage

| # | Stage | What Happens | Approval Needed? |
|---|-------|-------------|-----------------|
| 0c | **Requirement Intake** | BusinessAnalyst drafts `draft-REQ-XXXX.md` with clarifying questions; you answer in-file and rename to `REQ-XXXX.md`; BA validates and writes the Requirement Summary | **In-file** — human answers + rename |
| 0 | **Discovery** | ContextScout loads project standards, ExternalScout fetches library docs | No |
| 1 | **Product Discovery** | StoryMapper / ArchitectureAnalyzer / PrioritizationEngine / ProductOwner read the finalised `REQ-XXXX.md` and produce epics/stories with acceptance criteria | **Yes** — epic + story shape |
| 2 | **Sprint Planning** | ScrumMaster computes capacity, filters by DoR, proposes commitment | **Yes** — sprint commitment |
| 3 | **Init Session** | Binds execution to approved sprint, TaskManager creates TSK tickets | No |
| 4 | **Execute** | CoderAgent implements, BuildAgent validates, TestEngineer tests, TechnicalLead reviews, ProductOwner accepts | Per story |
| 5 | **Daily Standup** | ScrumMaster appends progress, burn-down, blockers to sprint file | No |
| 6 | **Validate & Triage** | Full DoD enforcement; failures → BUG-XXXX with RCA | On defects |
| 7 | **Sprint Review** | Done vs Committed, velocity tracking | No |
| 8 | **Retrospective** | Start/Stop/Continue + action items for next sprint | No |
| 9 | **Release** | ReleaseManager drafts release, collects sign-offs, updates CHANGELOG | **Yes** — release to production |

---

## 🛡️ Enterprise Rules & Quality Gates

OpenSDLC enforces these rules at all times:

| Rule | What It Means |
|------|--------------|
| **Traceability Gate** | No code without an active `TSK-XXXX` or `BUG-XXXX` ticket. Every change is traceable. |
| **Approval Gate** | Human PM approves before any sprint commitment, implementation, or release. |
| **Definition of Ready** | Stories must have clear description, Given/When/Then acceptance criteria, estimate, and identified dependencies before entering a sprint. |
| **Definition of Done** | Code merged + tests passing + code reviewed + docs updated + PO accepted + no P0/P1 defects open. |
| **Stop on Failure** | Test/build failures immediately create `BUG-XXXX.md` for triage. No silent auto-fixes. |
| **Separation of Duties** | CoderAgent never reviews its own code. Reviews go to TechnicalLead. Tests by TestEngineer. Acceptance by ProductOwner. |
| **Audit Trail** | Every state change appends a timestamped line to the ticket's Execution Log. |

---

## 💻 Example: End-to-End

```bash
opencode --agent OpenSDLC
> "Create an order management system with payment processing"
```

**1. Requirement Intake** — BusinessAnalyst writes `.sdlc/requirements/draft-REQ-0001-order-management.md` with ~8 clarifying questions (personas, in/out scope, KPIs, constraints, NFRs, edge cases, success criteria). You answer them inline and rename the file to `REQ-0001-order-management.md`. BA validates and appends the Requirement Summary.

**2. Discovery** — ContextScout finds your tech stack (e.g., Next.js + TypeScript + PostgreSQL)

**3. Product Discovery** — StoryMapper reads `REQ-0001` and produces:
- EPIC-0001: Order Management (linked to REQ-0001)
  - STY-0001: Create order with line items
  - STY-0002: Payment processing integration
  - STY-0003: Order status tracking
  - STY-0004: Email notifications on status change

Each story has Given/When/Then acceptance criteria derived from the REQ's Definition of Success.

**3. Sprint Planning** — ScrumMaster proposes:
```
Sprint 01 — "Core Order Flow"
Capacity: 21 points | Committed: 18 points
- STY-0001 (8pts) ✓ DoR met
- STY-0002 (5pts) ✓ DoR met
- STY-0003 (5pts) ✓ DoR met
- STY-0004 (3pts) ✗ DoR failed — notification service TBD

Approve? [y/n]
```

**4. You approve** → Sprint starts.

**5. Execution** — For STY-0001:
- TaskManager creates TSK-0001 through TSK-0004
- CoderAgent implements each task
- BuildAgent validates after each task
- TestEngineer authors tests against the acceptance criteria
- TechnicalLead performs independent review
- ProductOwner checks against Given/When/Then → **Accepted**
- STY-0001 status → Done

**6. Sprint Review** — velocity = 18pts, 3/3 stories Done

**7. Retro** — ScrumMaster writes Start/Stop/Continue

**8. Release** — ReleaseManager drafts REL-1.0.0 with sign-offs

---

## ⚙️ Configuration

### Model Configuration (Optional)

All agents use your OpenCode default model. To configure per-agent:

```bash
nano .opencode/agent/core/opensdlc.md
```

Change the model in the frontmatter:
```yaml
---
model: anthropic/claude-sonnet-4-5
---
```

### Add Your Coding Patterns

```bash
opencode
> /add-context
```

Answer 6 questions about your tech stack, patterns, and standards. All subagents will follow them automatically.

### Customize Agent Behavior

Every agent is an editable markdown file:

```bash
# Edit the orchestrator
nano .opencode/agent/core/opensdlc.md

# Edit Scrum roles
nano .opencode/agent/subagents/planning/scrum-master.md
nano .opencode/agent/subagents/planning/product-owner.md
nano .opencode/agent/subagents/planning/release-manager.md
```

Add project-specific rules, adjust DoD criteria, change sprint cadence — all in plain text.

---

## ❓ FAQ

**Q: What languages are supported?**  
A: Agents are language-agnostic. Primarily tested with TypeScript/Node.js. Python, Go, Rust, C# are supported. The SDLC process works with any language.

**Q: What models work?**  
A: Any model from any provider — Claude, GPT, Gemini, local models. No vendor lock-in.

**Q: Does this work on Windows?**  
A: Yes. Use Git Bash (recommended) or WSL.

**Q: Can I skip ceremonies I don't need?**  
A: Daily standups are optional. Everything else (planning, review, retro) can be invoked on demand rather than on a cadence. Edit `opensdlc.md` to adjust.

**Q: Where do the `.sdlc/` files live?**  
A: In your project root. Commit them to git — they are your process documentation and audit trail.

**Q: Can I use OpenSDLC alongside OpenCoder?**  
A: Yes. Use `opencode --agent OpenSDLC` for features that need full Scrum process. Use `opencode --agent OpenCoder` for quick implementations that don't need sprint ceremonies.

**Q: What if a test fails during execution?**  
A: OpenSDLC immediately creates a `BUG-XXXX.md` with steps to reproduce, expected vs actual behavior, and a fix plan. It then pauses and asks you (the PM) how to triage — never silently auto-fixes.

**Q: How does separation of duties work?**  
A: CoderAgent writes code. TechnicalLead reviews it (never the same agent). TestEngineer writes tests. ProductOwner accepts. No agent can mark its own work as Done.

**Q: How do I actually drive OpenSDLC day-to-day on a new vs. existing project?**  
A: See the **[OpenSDLC User Manual](MANUAL.md)** — full Green Field and Brown Field workflows, approval gates, tiered failure handling, and reconciliation.

---

## 📚 Documentation

- **[MANUAL.md](MANUAL.md)** — User manual: Green Field & Brown Field workflows, daily ops, approval gates, failure tiers, RepoSync, FAQ
- **[.opencode/agent/core/opensdlc.md](.opencode/agent/core/opensdlc.md)** — The OpenSDLC agent definition (rules, templates, workflow stages)
- **[docs/](docs/)** — Deep dives, agent catalogue, planning notes, contributing guides

---

## 🤝 Credits

OpenSDLC is built on top of [OpenAgents Control (OAC)](https://github.com/darrenhinde/OpenAgentsControl) by [Darren Hinde](https://x.com/DarrenBuildsAI), which extends the [OpenCode](https://opencode.ai) framework.

---

## License

MIT License.

---

**Built for teams that ship production software — not prototypes.**
