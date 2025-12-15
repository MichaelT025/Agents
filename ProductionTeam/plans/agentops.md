# AgentOps — Operational Rules for the AI Staff

This document defines **how the AI agents operate, communicate, and persist state**.
It is the *operational layer* that makes the TeamPlan workable in real projects.

This file answers:

* How agents communicate
* Where artifacts live
* What gets committed to git vs ignored
* How decisions, logs, and reviews are stored

---

## 1. Core Principle

> **Agents share artifacts, not brains.**

* No agent has shared memory with another agent
* No agent talks directly to another agent
* All coordination flows through the **Conductor**

This prevents hidden assumptions, context bleed, and untraceable decisions.

---

## 2. Communication Model

### Primary rule: Conductor-mediated coordination

By default, **all coordination flows through the Conductor**. This ensures:

* Clear ownership
* Debuggability when things go wrong
* A single source of truth for decisions

However, **a narrow, controlled exception is allowed** for efficiency.

### Allowed communication paths

```
You ↔ Conductor
Conductor → Agent
Agent → Conductor
```

### Controlled exception: Forge ↔ Sentinel iteration loop

For **bug fixing and review iteration only**, Forge and Sentinel may communicate **directly** under the following strict conditions:

* Scope: fixing issues already identified by Sentinel
* No new features
* No architectural changes
* No scope expansion
* Max 3 iterations

This loop exists to reduce coordination overhead for routine fixes.

### Mandatory escalation

* If the issue is architectural → escalate to Conductor
* If iteration exceeds 3 cycles → escalate to Conductor
* If Forge and Sentinel disagree on resolution → escalate to Conductor

### Forbidden communication paths

* ❌ Blueprint ↔ Forge (except via Conductor)
* ❌ Blueprint ↔ Sentinel
* ❌ Any agent making unilateral scope or design decisions

---

## 3. System Prompt Rules (Mandatory)

Every non-Conductor agent must be explicitly told:

* All instructions come **only** from the Conductor
* All outputs must be returned **only** to the Conductor
* The agent must not invent requirements or scope

Example clause (must exist in every agent prompt):

> You do not communicate with other agents directly.
> You only accept tasks from the Conductor.
> You return results as artifacts, not conversation.

---

## 4. Artifact-Driven Workflow

Agents do not hand off thoughts — they hand off **files**.

### Canonical artifact types

* PRDs
* Architecture specs
* Decision records (ADRs)
* Code diffs
* Review / QA reports
* Execution logs

Artifacts are **first-class project assets**.

---

## 5. Repository Structure (Canonical)

```
/docs
  /prd
  /architecture
  /decisions
  /reviews
  /logs
```

### Ownership by agent

| Folder               | Owner      | Contents                                |
| -------------------- | ---------- | --------------------------------------- |
| `/docs/prd`          | Conductor  | Product requirements, scope, milestones |
| `/docs/architecture` | Blueprint  | System designs, schemas, contracts      |
| `/docs/decisions`    | Conductor  | ADRs, tradeoffs, overrides              |
| `/docs/reviews`      | Sentinel   | QA, code review, security findings      |
| `/docs/logs`         | All agents | Short execution summaries               |

---

## 6. Where Each Agent Writes

### Conductor

* `/docs/prd/`
* `/docs/decisions/`
* Maintains overall project state

### Blueprint (Architect)

* `/docs/architecture/`
* One file per major design or revision
* Files are versioned, never overwritten

### Forge (Builder)

* Code changes
* Unit tests
* Short execution log in `/docs/logs/forge-log.md`

### Sentinel (Reviewer / QA / Security)

* `/docs/reviews/`
* One review document per review pass
* Includes verdict: Pass / Conditional / Fail

---

## 7. Execution Logs (Accountability)

Each agent writes a **short, human-readable log** after execution.

### Location

```
/docs/logs/
```

### Log format (strict)

```md
## YYYY-MM-DD — Task Name

Task:
- What was requested

Artifacts used:
- List of input artifacts

Actions taken:
- Bullet list

Open issues:
- None / List

Notes:
- Optional
```

Logs are **summaries**, not transcripts.

---

## 8. Review Artifacts (Sentinel)

Each Sentinel run produces a review document:

```
/docs/reviews/sentinel-review-<project>-<version>.md
```

### Must include

* Scope reviewed
* Test plan
* Findings (severity-ranked)
* Security notes (if applicable)
* Verdict: Pass / Conditional / Fail

Sentinel **never fixes code**.

---

## 9. Decision Records (ADRs)

When a decision is made that affects structure, behavior, or risk:

* It is recorded as an ADR
* Stored in `/docs/decisions/`

Example:

```
adr-0003-storage-format.md
```

ADR contents:

* Context
* Decision
* Alternatives considered
* Consequences

---

## 10. Escalation Rules (Explicit)

Escalation paths are **mandatory and explicit**. No silent deadlocks.

### Escalation cases

* Forge discovers the spec is incomplete, contradictory, or unimplementable
* Sentinel identifies a **design-level** flaw, not a local bug
* Forge and Sentinel disagree on readiness
* Forge ↔ Sentinel iteration exceeds 3 cycles

### Escalation target

All escalations go to the **Conductor**.

The Conductor may:

* Clarify or amend requirements
* Invoke Blueprint for redesign
* Accept risk and override Sentinel
* Defer or cut scope

Escalation decisions are recorded as ADRs in `/docs/decisions/`.

---

## 11. Iteration Limits

To prevent infinite loops and agent thrashing:

* Forge ↔ Sentinel may iterate **up to 3 times** on the same issue
* Each iteration must strictly reduce the issue set
* No new scope may be introduced during iteration

### On exceeding limits

* Automatic escalation to Conductor
* Conductor decides: redesign, defer, override, or stop

Iteration limits are guardrails, not failure conditions.

---

## 12. Git Policy

### MUST be committed

* `/docs/**`
* All artifacts, specs, reviews, ADRs, logs

### MUST be gitignored

* Raw LLM transcripts
* Agent caches
* Temporary state

Recommended `.gitignore`:

```gitignore
.agent-cache/
.agent-tmp/
.raw-transcripts/
.learn/
.tmp/
```

---

## 13. Artifact Retention Rule

> If an artifact influenced code or decisions, it stays.

Artifacts are never deleted — only superseded.

This enables:

* Debugging
* Auditing
* Knowledge transfer

---

## 14. Mental Model

* **Conductor** = OS kernel
* **Agents** = user-space programs
* **Artifacts** = files on disk
* **Repo** = memory + disk
* **You** = sysadmin

---

## Final Rule

> **If it mattered, write it down. If it didn’t, ignore it.**

This is what keeps the system scalable, debuggable, and sane.
