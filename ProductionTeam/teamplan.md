# End-to-End Production AI Team (Universal)

This document defines a **universal, reusable build-and-ship AI staff** for production-grade software.
It is designed to work across **any project** (desktop, web, backend, ML, tooling) and across **any AI IDE** (OpenCode, Windsurf, Cursor, CLI).

The system is:

* **Phase-based** (you call agents at specific moments)
* **Artifact-driven** (agents hand off specs, diffs, tests — not thoughts)
* **Cost-optimized** (frontier models only where judgment matters)

> You (the human) are the **Product Owner**. The AI staff executes.

---

## Authority Model

### 🔑 Orchestrator is the only agent with access to all others

* All other agents are **isolated by default**
* Orchestrator:

  * Decides *when* to call agents
  * Decides *which artifacts* get handed off
  * Resolves conflicts between agent outputs

This prevents chaos and keeps the system scalable.

---

## The 6 Core Agents

## 1) Orchestrator (Staff Engineer / PM Hybrid)

### Description with purpose

Owns end-to-end delivery. Converts ideas into a plan, assigns agents, enforces done-ness.

### Role

* Decompose ideas into executable work
* Sequence tasks and agents
* Enforce definition-of-done

### Recommended models

* **GPT-5.2 Thinking / Pro**
* **Claude Sonnet (latest)**

### Why these models

* Strong long-horizon reasoning
* Excellent at tradeoffs and prioritization
* Low hallucination for planning

### Detailed responsibilities

* Turn a vague idea into an MVP and milestones
* Write tickets with acceptance criteria
* Decide if Architect is required for a ticket
* Dispatch tasks to other agents with hard constraints
* Validate outputs and decide merge order
* Decide ship/no-ship

### Recommended names (wide range)

* **Conductor** — coordinates the orchestra
* **Foreman** — keeps work moving
* **Quartermaster** — allocates resources
* **Helmsman** — steers the project
* **Director** — calls the shots
* **AirTraffic** — prevents collisions
* **Roadmapper** — organizes the path
* **Integrator** — merges and resolves

---

## 2) Architect (System & API Designer)

### Description with purpose

Designs systems that are buildable and understandable *before* code is written.

### Role

* Own boundaries, contracts, and tradeoffs
* Prevent architectural debt

### Recommended models

* **GPT-5.2 Medium**
* **Claude Sonnet (latest)**

### Why these models

* Excellent structural reasoning
* Conservative and precise
* Strong at failure-mode analysis

### Detailed responsibilities

* Define components and data flow
* Design APIs / IPC / schemas
* Specify invariants and non-goals
* Identify scaling/security risks
* Produce spec-lite docs (goals, non-goals, acceptance)

### Recommended names

* **Blueprint** — everything is built from it
* **Keystone** — holds structure together
* **Atlas** — carries architectural weight
* **Cartographer** — maps before building
* **Schema** — contracts first
* **Boundary** — modular discipline
* **Wireframe** — structure before details
* **Civitas** — city-planner mindset

---

## 3) Implementer (Primary Builder)

### Description with purpose

Turns specs into working software quickly and correctly.

### Role

* Write production code from specs
* Avoid redesign; execute

### Recommended models

* **DeepSeek (latest coder / reasoning-coder)**
* **Qwen Coder (latest) local or cloud**

### Why these models

* Strong code generation
* Cheap and deterministic
* Excellent spec followers

### Detailed responsibilities

* Implement features end-to-end
* Keep diffs small and scoped
* Fix bugs with minimal blast radius
* Provide clear test instructions
* Write minimal docs/comments where needed

### Recommended names

* **Forge** — code is shaped through work
* **Mechanic** — precise execution
* **Craft** — quality-focused building
* **Operator** — executes plans
* **Assembler** — turns parts into systems
* **Bricklayer** — feature-by-feature
* **Hands** — pragmatic builder
* **Driver** — keeps momentum

---

## 4) Refactorer (Code Quality Engineer)

### Description with purpose

Improves code health without changing behavior.

### Role

* Reduce complexity
* Improve readability and performance

### Recommended models

* **Qwen Coder (local)**
* **DeepSeek Coder (local)**

### Why these models

* Pattern-matching excellence
* Zero cost when local
* Low risk of over-design

### Detailed responsibilities

* Remove duplication
* Improve naming and structure
* Simplify logic
* Extract utilities
* Guarantee no behavior change

### Recommended names

* **Polisher** — refines rough edges
* **Custodian** — maintains health
* **Hygiene** — keeps code clean
* **Gardener** — prunes and shapes
* **Tuner** — performance tweaks
* **Janitor** — removes clutter
* **Archivist** — organizes structure
* **Mason** — strengthens foundations

---

## 5) QA / Test Engineer

### Description with purpose

Proves the system works and prevents regressions.

### Role

* Think adversarially
* Guard correctness

### Recommended models

* **Gemini (latest Pro / reasoning tier)**
* **Kimi (latest long-context)**

### Why these models

* Excellent at reading large diffs
* Strong edge-case discovery
* Cost-effective for review/testing

### Detailed responsibilities

* Create test plans (happy + edge cases)
* Write unit/integration/E2E tests
* Produce minimal bug repro steps
* Verify fixes against acceptance criteria
* Identify flaky tests and instability

### Recommended names

* **Probe** — actively tests
* **Inspector** — correctness first
* **Verifier** — proves claims
* **Tripwire** — catches regressions
* **CrashTest** — tries to break things
* **RedTeamQA** — adversarial mindset
* **Checklist** — systematic validation
* **Harvester** — extracts edge cases

---

## 6) Release / DevOps Engineer

### Description with purpose

Makes software shippable, repeatable, and safe.

### Role

* Own CI/CD and packaging
* Reduce operational risk

### Recommended models

* **GPT-5.2 Instant / Medium**
* **Gemini Flash (latest)**

### Why these models

* Strong procedural reasoning
* Deterministic and reliable
* Cost-efficient

### Detailed responsibilities

* Create/maintain CI pipelines
* Manage build scripts and packaging
* Produce release checklists
* Manage environment config guidance
* Draft release notes and rollback plan

### Recommended names

* **Launchpad** — where software takes off
* **Pipeline** — code to production flow
* **Airlock** — safe transition
* **Relay** — controlled handoff
* **Dockmaster** — manages delivery
* **Packager** — artifacts and installers
* **Deployer** — rollout mindset
* **Shipwright** — builds ships that sail

---

# When to Call Each Agent (End-to-End)

## Phase A — Ideation → MVP Definition

1. **You → Orchestrator** (always)
2. **Orchestrator → Architect** (if any cross-module decisions, persistence, auth, provider integrations, IPC, or scaling)

## Phase B — Project Skeleton

1. **Orchestrator → Implementer** (scaffold + minimal end-to-end path)
2. **Orchestrator → Release** (dev scripts + CI stub)

## Phase C — Feature Loop (repeat per feature)

1. **You → Orchestrator** (create ticket)
2. **Orchestrator → Architect** (only if contracts/boundaries change)
3. **Orchestrator → Implementer** (build)
4. **Orchestrator → QA** (break/test)
5. **Orchestrator → Refactorer** (cleanup)
6. **Orchestrator → Release** (if shipping/build artifacts change)

## Phase D — Pre-Release Gate

1. **Orchestrator → QA** (full regression)
2. **Orchestrator → Release** (package + release checklist)
3. **You → Orchestrator** (final ship decision)

---

# Example Use Case (Full, Tangible, End-to-End)

Below is a complete example starting from the user (you) with concrete prompts.

## Project Example: “ClipNote” — a small desktop app that watches your clipboard and lets you save snippets into named collections.

### Goal (what you want)

* Runs on Windows
* Has a tray icon
* Saves clipboard snippets
* Lets you search and copy them back
* Ships as an installer

---

## Step 0 — You kick off the project

### ✅ You → Orchestrator (prompt)

**Command / prompt you give:**

> You are the Orchestrator. I want to build “ClipNote”, a Windows desktop clipboard manager.
>
> Constraints:
>
> * MVP in 2 days
> * Must run on Windows
> * Local storage only
> * Minimal UI, keyboard-first
>
> Output required:
>
> 1. MVP definition (5–7 bullets)
> 2. Milestones (M0 bootstrap, M1 MVP, M2 polish)
> 3. Ticket list with priorities and acceptance criteria
> 4. Identify which tickets require the Architect

**Expected output:**

* A small roadmap + a prioritized list of tickets.

---

## Step 1 — Orchestrator requests architecture (only once upfront)

### ✅ Orchestrator → Architect (prompt)

> You are the Architect. Design the MVP architecture for ClipNote.
>
> Requirements:
>
> * Desktop app (tray)
> * Clipboard watcher
> * Local persistence
> * Search UI
>
> Output required:
>
> 1. Module boundaries + folder map
> 2. Data model (schemas)
> 3. IPC/API contracts (if applicable)
> 4. Failure modes and non-goals
> 5. “Definition of Done” for the architecture

**Expected output:**

* A spec-lite document with clear contracts and file boundaries.

---

## Step 2 — Scaffold the project (skeleton)

### ✅ You → Orchestrator (prompt)

> Orchestrator: create the scaffold plan. I want a minimal end-to-end vertical slice that:
>
> * starts the app
> * runs in tray
> * shows a window
> * displays “Watching clipboard…”
>
> Then dispatch Implementer and Release.

### ✅ Orchestrator → Implementer (prompt)

> You are the Implementer. Implement the scaffold vertical slice exactly.
>
> Constraints:
>
> * Follow Architect’s folder map
> * No redesign
> * Keep diffs small
>
> Output required:
>
> * Code changes
> * How to run locally
> * Manual test steps

### ✅ Orchestrator → Release (prompt)

> You are Release/DevOps. Set up:
>
> * dev scripts
> * basic CI (lint + test + build)
> * release checklist draft
>
> Output required:
>
> * CI config
> * commands to run
> * what files were added/changed

---

## Step 3 — Feature loop example: “Persist clipboard snippets”

### ✅ You → Orchestrator (prompt)

> Orchestrator: create a ticket for feature “Persist clipboard snippets”.
>
> Requirements:
>
> * When clipboard changes, append a snippet record
> * Store locally
> * Keep last 2000
>
> Output required:
>
> * Acceptance criteria
> * Test plan
> * Files touched
> * Decide if Architect is needed

### ✅ If Orchestrator says Architect is needed (schema/contract)

**Orchestrator → Architect (prompt)**

> Architect: define the persistence schema and storage abstraction.
>
> Output required:
>
> * schema + migration strategy (even if trivial)
> * interface for storage module
> * failure modes

### ✅ Orchestrator → Implementer (prompt)

> Implementer: implement the persistence feature.
>
> Constraints:
>
> * Use Architect’s schema + interface
> * No UI beyond minimal indicators
> * Add unit tests where feasible
>
> Output required:
>
> * Code diff
> * How to test
> * Any new config/env requirements

### ✅ Orchestrator → QA (prompt)

> QA: attempt to break “Persist clipboard snippets”.
>
> Provide:
>
> * edge cases (large clipboard, rapid changes, non-text)
> * reproducible bug steps
> * tests to add
> * pass/fail vs acceptance criteria

### ✅ Orchestrator → Refactorer (prompt)

> Refactorer: cleanup ONLY.
>
> Constraints:
>
> * No behavior changes
> * Improve naming, structure, remove duplication
> * Keep diff minimal
>
> Output required:
>
> * A list of changes
> * The refactor diff

---

## Step 4 — Pre-release

### ✅ You → Orchestrator (prompt)

> Orchestrator: prepare for v0.1 release.
>
> Output required:
>
> * release checklist
> * full regression plan
> * assign QA and Release

### ✅ Orchestrator → QA (prompt)

> QA: run a regression plan for MVP.
>
> Output:
>
> * checklist with pass/fail
> * top 5 risks
> * last-minute fixes (if any)

### ✅ Orchestrator → Release (prompt)

> Release: package the app and produce a release artifact.
>
> Output:
>
> * packaging steps
> * installer generation
> * release notes draft
> * rollback notes

### ✅ You → Orchestrator (final ship prompt)

> Orchestrator: based on QA + Release outputs, recommend SHIP / NO-SHIP.
> If NO-SHIP, list the minimum fixes required and reassign.

---

# Notes on Using This in OpenCode

* You (human) only talk to **Orchestrator** most of the time.
* Orchestrator calls other agents and hands them:

  * the ticket
  * relevant files
  * the acceptance criteria
  * any architecture contracts

---

# Core Rule

> **Agents do not share a brain. They share artifacts.**

If you enforce artifacts (tickets/specs/diffs/tests), model switching and multi-agent work stays coherent and cheap.
