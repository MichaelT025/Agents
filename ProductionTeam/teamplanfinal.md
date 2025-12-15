# TeamPlanFinal — Universal End-to-End Production AI Team

This document defines the **final, canonical AI staff** for building and shipping software.
It balances **minimal coordination overhead** with **strong quality guarantees**, and scales from solo projects to complex systems.

**Default philosophy:**

* Fewer agents by default
* Clear ownership
* Artifact-based handoffs
* Specialists are *summoned*, not permanent

> You (the human) are the **Product Owner**.
> The **Conductor** is the only agent you talk to directly most of the time.

---

## Final Core Team (4 Agents)

| # | Agent         | Purpose                            |
| - | ------------- | ---------------------------------- |
| 1 | **Conductor** | Plan, coordinate, decide, ship     |
| 2 | **Blueprint** | Design systems & contracts         |
| 3 | **Forge**     | Build clean, tested software       |
| 4 | **Sentinel**  | Review, QA, security, verification |

This team covers **90%+ of real software work**.

---

## Authority Model (Critical)

### 🔑 Conductor is the single authority

* Only agent with access to all others
* Owns sequencing, scope, and decisions
* Resolves conflicts between agents

All other agents:

* Work in isolation
* Receive **explicit artifacts**
* Do not coordinate with each other directly, **except** for the controlled **Forge ↔ Sentinel** iteration loop (quick fixes only, capped iterations)

This prevents role drift and chaos.

---

## 1) Conductor (Orchestrator / Director)

### Description with purpose

Owns the entire delivery lifecycle. Converts ideas into executable plans and decides what ships.

### Core role

* Turn ideas into PRDs, milestones, and tickets
* Decide which agents are needed and when
* Enforce acceptance criteria
* Make ship / no-ship decisions

### Detailed responsibilities

* Write PRDs (problem, user, scope, non-goals)
* Create phase-by-phase implementation plans
* Break phases into tickets with acceptance criteria
* Dispatch work to Blueprint / Forge / Sentinel
* Integrate feedback and unblock progress
* Own final decision making

### When to call

* **Always first** for any new work
* After any agent finishes, to decide next steps

### Does NOT do

* Write code
* Design architecture
* Test or review code

### Recommended models

* Claude Opus / GPT-5.2 Pro (judgment-heavy)
* Claude Sonnet / GPT-5.2 Medium (routine planning)

### Recommended names (wide range)

* **Conductor** — coordinates the orchestra
* **Director** — calls the shots
* **Helmsman** — steers the project
* **Quartermaster** — allocates resources
* **Integrator** — resolves handoffs
* **Roadmapper** — plans the path
* **AirTraffic** — prevents collisions

---

## 2) Blueprint (Architect / System Designer)

### Description with purpose

Designs systems *before* code is written. Owns structure, contracts, and tradeoffs.

### Core role

* Define how the system fits together
* Prevent architectural debt

### Detailed responsibilities

* Define module boundaries and data flow
* Design APIs, schemas, IPC, contracts
* Identify failure modes and risks
* Specify invariants and non-goals
* Produce lightweight specs (not novels)

### When to call

* New projects (initial structure)
* Features introducing:

  * persistence
  * auth
  * external integrations
  * new subsystems
* Major refactors affecting structure

### Skip when

* Bug fixes
* UI tweaks
* Copy/content changes
* Features following existing patterns

### Does NOT do

* Implement code
* Test code
* Manage tasks

### Recommended models

* Claude Opus / GPT-5.2 Medium
* Claude Sonnet (smaller decisions)

### Recommended names

* **Blueprint** — design before build
* **Keystone** — structural integrity
* **Atlas** — carries architectural weight
* **Cartographer** — maps the system
* **Schema** — contracts first
* **Boundary** — modular discipline
* **Wireframe** — structure before detail

---

## 3) Forge (Builder / Engineer)

### Description with purpose

Builds clean, working, tested software from specs. Implementation and refactoring are **one responsibility**.

### Core role

* Turn specs into production code
* Maintain code quality as work progresses

### Detailed responsibilities

* Implement features end-to-end
* Refactor while implementing (no sloppy passes)
* Write unit tests for non-trivial logic
* Fix bugs with minimal blast radius
* Keep diffs small and reviewable
* Add inline documentation where needed

### Quality standards (always enforced)

* No obvious duplication
* Clear naming
* Reasonable function/file size
* Tests for meaningful logic

### When to call

* All feature implementation
* Bug fixes
* Explicit refactor requests

### Does NOT do

* Redesign architecture
* Change contracts without Blueprint
* Coordinate other agents

### Recommended models

* Claude Sonnet (primary)
* DeepSeek Coder / Qwen Coder (cheap execution)
* Claude Opus (complex logic only)

### Recommended names

* **Forge** — craftsmanship
* **Craft** — quality-first builder
* **Mechanic** — precise execution
* **Assembler** — system construction
* **Bricklayer** — feature by feature
* **Operator** — gets it done
* **Smith** — disciplined maker

---

## 4) Sentinel (Reviewer / QA / Security)

### Description with purpose

Guards quality. Assumes the system is wrong, fragile, or exploitable until proven otherwise.

### Core role

* Verify correctness
* Review code
* Perform adversarial testing and security checks

### Detailed responsibilities

* Review code for correctness and maintainability
* Execute and design test plans
* Identify edge cases and regressions
* Perform security review (inputs, permissions, data exposure)
* Produce clear bug reports with repro steps
* Validate fixes against acceptance criteria

### When to call

* After Forge completes meaningful work
* Before releases
* When debugging tricky or recurring issues

### Skip when

* Trivial changes
* Internal scripts
* Low-risk prototypes (explicit risk accepted)

### Does NOT do

* Implement fixes
* Design systems
* Manage scope

### Recommended models

* Gemini Pro / Claude Sonnet (general review)
* Claude Opus (security-heavy review)
* Gemini / Kimi (large codebases)

### Recommended names

* **Sentinel** — vigilant guardian
* **Gatekeeper** — quality gate
* **Inspector** — correctness focus
* **Verifier** — proves claims
* **Tripwire** — catches regressions
* **Watchtower** — oversight
* **RedTeam** — adversarial mindset

---

## Optional / On-Demand Specialists

These are **not permanent agents**.
Summon only when needed.

* **Release / DevOps** — complex CI, packaging, deployments
* **Deep Security Review** — pre-public launch
* **Performance Analyst** — profiling and optimization
* **Documentation Writer** — user / API docs

---

# End-to-End Workflow

## Phase A — Kickoff

```
You → Conductor
Conductor → Blueprint (if non-trivial)
```

## Phase B — Feature Loop

```
You → Conductor
Conductor → Blueprint (if boundaries change)
Conductor → Forge
Forge → Conductor
Conductor → Sentinel
Sentinel → Conductor
(Loop until clean)
```

For routine fix-and-verify cycles, **Forge ↔ Sentinel may iterate directly** under AgentOps rules:

* Fixes only (no new features, no architectural changes)
* **Max 3 iterations**, then escalate to Conductor

## Phase C — Release

```
You → Conductor
Conductor → Sentinel (full verification)
Conductor → Forge (package / prep)
Conductor → You (ship decision)
```

---

# Example Use Case (Concrete)

## Project: "ClipNote" — Desktop Clipboard Manager

### Step 0 — Idea

**You → Conductor**

> I want to build ClipNote: a Windows clipboard manager.
> Constraints: MVP in 2 days, local-only storage, keyboard-first.
> Output: PRD, milestones, tickets.

---

### Step 1 — Architecture

**Conductor → Blueprint**

> Design MVP architecture.
> Include module boundaries, storage schema, and failure modes.

---

### Step 2 — Scaffold

**Conductor → Forge**

> Implement minimal vertical slice:
> app boots, tray icon, placeholder UI.

---

### Step 3 — Feature: Persist Snippets

**You → Conductor**

> Create ticket for "Persist clipboard snippets".

**Conductor → Blueprint** (schema needed)

> Define persistence schema and interface.

**Conductor → Forge**

> Implement persistence feature per spec.

**Conductor → Sentinel**

> Review, test edge cases, attempt to break it.

---

### Step 4 — Fixes & Polish

**Conductor → Forge**

> Fix Sentinel issues.

**Conductor → Sentinel**

> Verify fixes.

---

### Step 5 — Release

**You → Conductor**

> Prepare v0.1 release.

**Conductor → Sentinel**

> Full regression + security sanity check.

**Conductor → Forge**

> Package build and draft release notes.

**Conductor → You**

> Ship / No-Ship recommendation.

---

## Core Rule (Memorize This)

> **Agents do not share a brain. They share artifacts.**

This keeps the system scalable, cheap, and sane.

---

<!--
## Claude Opus 4.5 Comments (Review Notes)

Overall: This is a solid, production-ready team structure. The consolidation to 4 agents was the right call. A few observations and minor suggestions:

### What Works Well

1. **Authority model is clear.** Conductor-as-hub prevents the chaos of peer-to-peer agent communication. This is critical for debugging when things go wrong.

2. **"Skip when" sections are valuable.** Explicit skip conditions prevent over-engineering on simple tasks. Most agent frameworks fail here.

3. **Optional specialists pattern is correct.** Making Release/DevOps on-demand rather than permanent avoids the trap of invoking agents that have nothing to do.

4. **"Agents share artifacts, not brains"** — this is the key insight. Should be in every agent's system prompt.

### Minor Suggestions (Non-Blocking)

1. **Consider adding artifact templates.** The document describes *what* gets handed off but not *how*. A lightweight spec template for Blueprint→Forge handoffs would reduce ambiguity. Even a 5-field markdown template (Goal, Constraints, Schema, Files to touch, Acceptance criteria) would help.

2. **Escalation paths could be explicit.** What happens when:
   - Forge discovers the spec is incomplete mid-implementation?
   - Sentinel finds a fundamental design flaw, not just a bug?
   - Two agents disagree (e.g., Forge says "done", Sentinel says "broken")?

   Currently implied that Conductor resolves all, but explicit escalation rules would help.

3. **Model fallback chains.** The model recommendations are good, but consider documenting fallback behavior. If Opus is unavailable or rate-limited, does Conductor fall back to Sonnet automatically? This matters for CI/automation.

4. **Iteration limits.** The "loop until clean" in Phase B could theoretically infinite-loop. Consider a max iteration count (e.g., 3 Forge↔Sentinel cycles before escalating to human).

5. **Artifact retention.** Not mentioned: how long do specs, tickets, and review artifacts persist? For debugging "why did we build it this way?" later, artifact history matters. Could be as simple as "all artifacts committed to repo in /docs/decisions/".

### Edge Cases to Watch

- **Forge self-reviewing:** If Forge writes tests, who verifies the tests are good? Sentinel should review test quality, not just run tests.

- **Blueprint scope creep:** Architects love to architect. May need explicit word limits or time-boxing to prevent Blueprint from over-specifying.

- **Sentinel false positives:** An overly aggressive Sentinel can block progress. Consider a "Conductor override" mechanism for low-risk items Sentinel flags.

### Things I Would NOT Change

- The 4-agent count. Resist pressure to add more.
- Forge owning both implementation and refactoring. This was the right merge.
- Sentinel owning security. Too often security is nobody's job.
- The example walkthrough. Concrete examples are underrated in specs.

### Final Thought

This structure will work across project types (web, desktop, CLI, ML) without modification. The key to success will be **prompt engineering for each agent** — the system prompt for Forge should be very different from Sentinel's. The next step is writing those prompts.

— Claude Opus 4.5, December 2024
-->

