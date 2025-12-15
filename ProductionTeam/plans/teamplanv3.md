# End-to-End Production AI Team v3 (Revised)

This document revises the original 6-agent team plan with critical analysis and recommendations for a leaner, more effective structure.

---

## Executive Summary: Reduce to 4 Core Agents

**Original:** 6 agents (Orchestrator, Architect, Implementer, Refactorer, QA, Release)

**Recommended:** 4 agents (Orchestrator, Architect, Builder, Reviewer)

**Why:** The original plan has role overlap, artificial separations, and unnecessary complexity. A 4-agent structure maintains all capabilities while reducing coordination overhead and ambiguity.

---

## Critical Analysis of Original Plan

### 1. Role Overlap Issues

| Problem | Agents Involved | Analysis |
|---------|-----------------|----------|
| Implementer vs Refactorer | #3, #4 | Artificial separation. Good implementation includes clean code. Separating "write code" from "clean code" creates handoff friction and encourages sloppy first passes. |
| QA responsibilities scattered | #5, partially #1 | QA does testing AND reviews, but Orchestrator also "validates outputs." Who owns quality? |
| Architect vs Orchestrator | #1, #2 | Both make structural decisions. "Orchestrator decides if Architect is required" creates meta-overhead. |

### 2. Missing Responsibilities

| Gap | Impact |
|-----|--------|
| **Security review** | No agent explicitly owns security analysis, threat modeling, or vulnerability scanning |
| **Documentation** | Mentioned once ("minimal docs") but no ownership. Who writes user docs, API docs? |
| **Code review** | QA tests but who reviews code quality, patterns, and maintainability? |
| **Debugging complex issues** | Implementer "fixes bugs" but no explicit debugging/investigation capability |

### 3. Overengineering Concerns

- **Refactorer as standalone agent:** In practice, you refactor *while* implementing or *during* code review. A separate agent creates artificial workflow breaks.
- **Release agent for every project:** Many projects don't need custom CI/CD work. A solo dev project with `npm run build` doesn't need a DevOps agent.
- **6-agent coordination overhead:** Every additional agent adds handoff cost, context switching, and potential for miscommunication.

### 4. Ambiguity in When Agents Are Called

The original plan says:
- "Orchestrator → Architect (if any cross-module decisions...)" — but what counts as "cross-module"? Every non-trivial feature touches multiple files.
- "Orchestrator → Refactorer (cleanup)" — After *every* feature? That's expensive. Only when needed? Then who decides?
- Phase-based calling assumes linear development. Real work is iterative.

### 5. Scalability Across Projects

**Works well:**
- Artifact-driven handoffs
- Orchestrator as single entry point
- Phase-based structure (with caveats)

**Doesn't scale:**
- 6 agents for a simple CLI tool is overkill
- No guidance on when to skip agents
- Assumes every project needs architecture design

---

## Revised Team: 4 Core Agents

### Why 4?

- **Minimum viable team:** Orchestrator (coordinate), Architect (design), Builder (execute), Reviewer (verify)
- **Clear ownership:** Each agent has distinct, non-overlapping responsibilities
- **Scales down:** Small projects can skip Architect and Reviewer
- **Scales up:** Large projects can invoke Architect/Reviewer more frequently

---

## Agent 1: Orchestrator (The Director)

### Purpose
Owns the entire delivery lifecycle. Converts user intent into structured work, sequences execution, and makes ship decisions.

### Responsibilities
- Decompose ideas into milestones and tickets
- Write acceptance criteria for every task
- Decide which agents are needed per task
- Resolve conflicts between agent outputs
- Validate completion against acceptance criteria
- Make ship/no-ship decisions

### When to call
- **Always first** for any new work
- After any agent completes, to evaluate next steps

### Model recommendation
| Tier | Model | Reason |
|------|-------|--------|
| Primary | Claude Opus / GPT-5.2 Pro | Long-horizon reasoning, judgment calls |
| Fallback | Claude Sonnet / GPT-5.2 Medium | Cost-effective for routine coordination |

### Does NOT do
- Write code
- Design systems (delegates to Architect)
- Test (delegates to Reviewer)

### Recommended name
**Conductor** — clearly conveys coordination without implying coding

---

## Agent 2: Architect (The Designer)

### Purpose
Designs systems before code is written. Owns structure, contracts, and technical decisions.

### Responsibilities
- Define module boundaries and data flow
- Design APIs, schemas, and contracts
- Identify failure modes and security concerns
- Produce lightweight specs (not full documentation)
- Make build-vs-buy decisions
- Evaluate technical tradeoffs

### When to call
- **New projects:** Always, for initial structure
- **New features:** Only if they introduce new boundaries, persistence, auth, or external integrations
- **Refactoring:** When considering significant structural changes
- **Skip for:** Bug fixes, UI tweaks, copy changes, routine features within existing patterns

### Model recommendation
| Tier | Model | Reason |
|------|-------|--------|
| Primary | Claude Opus / GPT-5.2 Medium | Structural reasoning, conservative judgment |
| Fallback | Claude Sonnet | Cost-effective for smaller decisions |

### Does NOT do
- Implement code
- Test code
- Manage tickets

### Recommended name
**Blueprint** — clear metaphor for pre-implementation design

---

## Agent 3: Builder (The Engineer)

### Purpose
Turns specs into working, clean, tested software. This is a **merged role** combining Implementer + Refactorer from the original plan.

### Why merge Implementer and Refactorer?
1. **Good code is written clean the first time.** Separating "write" from "clean" encourages sloppy drafts.
2. **Context matters.** The agent that wrote the code understands it best for cleanup.
3. **Fewer handoffs.** One agent owns "code quality" end-to-end.
4. **Professionals don't ship messy code.** Real engineers refactor as they go.

### Responsibilities
- Implement features from specs
- Write unit tests alongside implementation
- Refactor for clarity and maintainability
- Keep diffs focused and reviewable
- Fix bugs with minimal blast radius
- Document complex logic inline

### Quality standards (built-in, not separate)
- No obvious duplication
- Clear naming
- Reasonable function/file sizes
- Tests for non-trivial logic

### When to call
- For every implementation task
- For bug fixes
- For isolated refactoring (when explicitly requested)

### Model recommendation
| Tier | Model | Reason |
|------|-------|--------|
| Primary | Claude Sonnet / DeepSeek Coder | Strong code generation, fast |
| Complex work | Claude Opus | When logic is intricate |
| Local option | Qwen Coder | Zero cost, good for routine work |

### Does NOT do
- Design systems (follows Architect's specs)
- Make structural decisions beyond current scope
- Coordinate other agents

### Recommended name
**Forge** — implies quality craftsmanship, not just output

---

## Agent 4: Reviewer (The Critic)

### Purpose
Proves the system works, catches problems, and guards quality. This is an **expanded role** combining QA + code review + security review.

### Why expand QA into Reviewer?
1. **Quality is holistic.** Tests, code review, and security review are all "verification."
2. **Adversarial mindset applies everywhere.** Finding bugs in tests, code, and security requires the same skepticism.
3. **Single owner for "does this work?"** eliminates ambiguity.

### Responsibilities
- Review code for correctness, patterns, and maintainability
- Write and execute test plans (unit, integration, E2E)
- Perform security review (OWASP top 10, auth flows, input validation)
- Find edge cases and failure modes
- Verify fixes against acceptance criteria
- Produce clear bug reports with repro steps

### When to call
- After Builder completes any significant work
- Before any release
- When investigating production issues
- **Skip for:** Trivial changes, copy updates, config tweaks

### Model recommendation
| Tier | Model | Reason |
|------|-------|--------|
| Primary | Gemini Pro / Claude Sonnet | Large context for code review, good at edge cases |
| Security focus | Claude Opus | Deeper reasoning for threat modeling |
| Long files | Gemini / Kimi | Large context windows |

### Does NOT do
- Implement features (reports issues, doesn't fix them)
- Design systems
- Manage project

### Recommended name
**Sentinel** — implies vigilance and protection

---

## What About Release/DevOps?

### Recommendation: Demote to Optional/On-Demand

**Reasoning:**
1. Most projects use standard CI templates (GitHub Actions, etc.)
2. Builder can write basic CI configs
3. Release work is infrequent (not per-feature)
4. Adding a 5th agent increases complexity for rare use

**When you DO need dedicated Release work:**
- Custom packaging (installers, containers)
- Complex deployment pipelines
- Multi-environment management
- Release automation

**Solution:** The Builder agent handles routine CI/release tasks. For complex DevOps work, invoke a specialized prompt to the Builder with DevOps context, or use a temporary 5th agent for that project.

---

## Revised Workflow

### Phase A: Project Kickoff

```
You → Conductor: "I want to build X with constraints Y"
     ↓
Conductor → Blueprint: "Design the system" (if non-trivial)
     ↓
Blueprint → Conductor: Returns spec document
     ↓
Conductor → You: "Here's the plan and tickets"
```

### Phase B: Feature Loop

```
You → Conductor: "Build feature X"
     ↓
Conductor → Blueprint: "Review contracts" (only if boundaries change)
     ↓
Conductor → Forge: "Implement per spec"
     ↓
Forge → Conductor: Returns code + tests
     ↓
Conductor → Sentinel: "Review and test"
     ↓
Sentinel → Conductor: Returns verdict + issues
     ↓
(If issues) Conductor → Forge: "Fix issues"
     ↓
Conductor → You: "Feature complete"
```

### Phase C: Release

```
You → Conductor: "Prepare release"
     ↓
Conductor → Sentinel: "Full regression"
     ↓
Conductor → Forge: "Package and prep release notes"
     ↓
Conductor → You: "Ship/No-ship recommendation"
```

---

## When to Scale Up or Down

### Minimal Team (Solo/Simple Projects)
- **Use only:** Conductor + Forge
- **Skip:** Blueprint (for simple apps), Sentinel (for low-risk internal tools)
- **Example:** CLI tool, personal script, prototype

### Standard Team (Most Projects)
- **Use:** Conductor + Blueprint + Forge + Sentinel
- **Example:** Web app, desktop app, API service

### Extended Team (Complex Projects)
- **Add:** Dedicated Release agent for complex deployments
- **Add:** Multiple Forge instances for parallel work
- **Example:** Distributed system, enterprise software

---

## Comparison: Original vs Revised

| Aspect | Original (6 agents) | Revised (4 agents) |
|--------|--------------------|--------------------|
| Coordination overhead | High | Low |
| Role clarity | Overlap between Impl/Refactor, Orch/Arch | Clear separation |
| Security ownership | None explicit | Sentinel owns it |
| Code review | Ambiguous | Sentinel owns it |
| Minimum viable team | 3? 4? Unclear | 2 (Conductor + Forge) |
| Handoffs per feature | 5-6 | 3-4 |
| Cost per feature | Higher (more agents) | Lower |

---

## Final Agent Summary

| # | Name | Role | Model Tier |
|---|------|------|------------|
| 1 | **Conductor** | Coordinate, plan, decide | Frontier (Opus/GPT-5.2) |
| 2 | **Blueprint** | Design systems | Frontier/Mid |
| 3 | **Forge** | Build + refactor + test | Mid/Fast (Sonnet/DeepSeek) |
| 4 | **Sentinel** | Review + QA + security | Mid (Gemini/Sonnet) |

---

## Additional Recommendations

### 1. Define Clear Handoff Artifacts

Every agent-to-agent handoff should include:
- **Conductor → Blueprint:** Requirements doc, constraints, non-goals
- **Blueprint → Forge:** Spec with schemas, contracts, file structure
- **Forge → Sentinel:** Code diff, test instructions, acceptance criteria
- **Sentinel → Conductor:** Test results, issues found, pass/fail verdict

### 2. Create Skip Conditions

Document when to skip agents:
- **Skip Blueprint:** Bug fixes, UI polish, routine features matching existing patterns
- **Skip Sentinel:** Trivial changes, internal tooling, time-critical hotfixes (with explicit risk acceptance)

### 3. Handle Iteration

Real development isn't linear. Define:
- When Sentinel finds issues → back to Forge (not full loop restart)
- When Forge discovers design problems → escalate to Blueprint
- When requirements change mid-feature → back to Conductor

### 4. Cost Controls

- Use cheaper models (Sonnet, DeepSeek) for routine work
- Reserve Opus/GPT-5.2 Pro for judgment calls
- Batch similar tasks to reduce context switching

---

## Conclusion

The original 6-agent team is well-intentioned but overengineered for most use cases. The revised 4-agent team (Conductor, Blueprint, Forge, Sentinel) provides:

1. **Clear ownership** — no overlap between agents
2. **Fewer handoffs** — less coordination overhead
3. **Explicit quality ownership** — Sentinel owns all verification
4. **Flexibility** — scales from 2 to 5 agents based on project needs
5. **Security included** — not an afterthought

The key insight: **merge roles that share context** (Implementer + Refactorer → Forge) and **expand roles that share mindset** (QA + Code Review + Security → Sentinel).

---

## Migration from v2

If you're already using the 6-agent structure:

1. Merge Implementer + Refactorer prompts → Forge
2. Expand QA prompts to include code review and security → Sentinel
3. Update Orchestrator to become Conductor (same role, clearer name)
4. Keep Architect as Blueprint (same role)
5. Deprecate Release as standalone; fold into Forge or invoke on-demand
