# Agent Orchestration System

This directory defines how agents collaborate to take a project from SRS (Software Requirements Specification) through implementation and review.

**⚠️ IMPORTANT**: This system is for **feature development only** (SRS → Spec → Implement → Review).

For **architectural decisions** (tech choices, system design, ADRs), use the separate agent: [../.github/agents/plan.agent.md](../plan.agent.md)

---

## When to Use This System vs. Other Agents

| Need | Agent/System | Location |
|------|---|----------|
| Developing a new feature (SRS to production) | **Orchestration System** | This directory (`orchestration/`) |
| Building components/services quickly | Domain instructions + prompts | `.github/instructions/` + `.github/prompts/` |
| Making architectural decisions | plan.agent (Architecture Decision Maker) | `../.github/agents/plan.agent.md` |
| Fixing bugs or quick improvements | Domain instructions + prompts | `.github/instructions/` + `.github/prompts/` |

---

## Workflow Overview

```
┌─────────────┐
│  SRS Input  │  (Human-provided Software Requirements Specification)
└──────┬──────┘
       │
       ↓
┌─────────────────────────────────────┐
│  Project Planner Agent              │
│  (per project: backend-api or       │
│   frontend-web)                     │
│                                     │
│  Reads: SRS, ER diagrams,           │
│          sequence diagrams          │
│  Produces: Feature specs,           │
│            Implementation guides,   │
│            ER diagrams updated,     │
│            Acceptance criteria      │
└──────┬──────────────────────────────┘
       │
       ├─────────────────────────────────────┐
       │                                     │
       ↓                                     ↓
┌────────────────────────┐      ┌────────────────────────┐
│ Backend Implementation │      │ Frontend Implementation│
│ Agent                  │      │ Agent                  │
│                        │      │                        │
│ Receives: Backend spec │      │ Receives: Frontend spec│
│          Tech stack    │      │          Tech stack    │
│          API contracts │      │          UI spec       │
│          DB schema     │      │          State mgmt    │
│                        │      │          Components    │
│ Produces: Code +       │      │ Produces: Code +       │
│           Tests +      │      │           Tests +      │
│           PR           │      │           PR           │
└────────┬───────────────┘      └────────┬───────────────┘
         │                               │
         │    (both complete)            │
         └───────────┬───────────────────┘
                     ↓
         ┌─────────────────────┐
         │  Review/QA Agent    │
         │                     │
         │  Checks: Spec match │
         │          Test cover │
         │          Quality    │
         │          Security   │
         │          Perf perf  │
         │                     │
         │  Output: Approval + │
         │          Feedback   │
         └────────┬────────────┘
                  ↓
         ┌─────────────────────┐
         │  Ready for Merge    │
         │  (Human approval)   │
         └─────────────────────┘
```

## Agent Roles

| Agent | Responsibilities | Triggers | Handoff To |
|-------|------------------|----------|-----------|
| **Project Planner** | Parse SRS → Create specs, diagrams, implementation guides | When new SRS received | Implementation agents |
| **Backend Implementation** | Code implementation following backend spec | When backend spec ready | Review Agent |
| **Frontend Implementation** | Code implementation following frontend spec | When frontend spec ready | Review Agent |
| **Review/QA Agent** | Verify implementation matches spec, quality checks | Both implementations done | Merge approval |

## Handoff Protocol

Each handoff must include:

1. **Input Template** — What the receiving agent gets
2. **Output Template** — What the agent must deliver
3. **Success Criteria** — When handoff is complete
4. **Quality Gates** — Blocking issues before proceeding
5. **Escalation Path** — If agent can't proceed

See [HANDOFF-PROTOCOL.md](./HANDOFF-PROTOCOL.md) for details.

## Agent Files

- **Project Planner**: [project-planner.agent.md](./project-planner.agent.md)
- **Backend Implementation**: [backend-implementation.agent.md](./backend-implementation.agent.md)
- **Frontend Implementation**: [frontend-implementation.agent.md](./frontend-implementation.agent.md)
- **Review/QA**: [review-qa.agent.md](./review-qa.agent.md)

## Starting a New Feature

### Step 1: Create SRS Input
Write SRS document at: `docs/projects/{project}/srs/{feature-name}/SRS.md`

Use template: [SRS-input-template.md](../../templates/handoffs/srs-input-template.md)

### Step 2: Hand Off to Planner
Reference: [project-planner.agent.md](./project-planner.agent.md)

```
I need to plan this feature for {project}:

**SRS**: [docs/projects/{project}/srs/{feature-name}/SRS.md](...)
**Project Context**: [docs/projects/{project}/context.md](...)
**Project Memory**: [docs/projects/{project}/memory.md](...)

Analyze and produce:
1. Feature specification
2. Backend implementation guide
3. Frontend implementation guide
4. Updated ER diagrams
5. Sequence diagrams
6. Acceptance criteria
```

### Step 3: Implementation (Parallel)
Both frontend and backend agents work in parallel:

**Backend**: [backend-implementation.agent.md](./backend-implementation.agent.md)  
**Frontend**: [frontend-implementation.agent.md](./frontend-implementation.agent.md)

### Step 4: Review & Approval
Reference: [review-qa.agent.md](./review-qa.agent.md)

```
Review these implementations:

**Backend PR**: [link]
**Frontend PR**: [link]
**Original Spec**: [link]
**Acceptance Criteria**: [link]

Verify all criteria met and approve or request changes.
```

## Monitoring Handoffs

Track each handoff in `docs/projects/{project}/memory.md`:

```markdown
### [Date] — Feature X Handoff

**Planner → Implementation**
- Specs complete: ✅
- Diagrams clear: ✅
- Questions raised: None
- Status: Handed off to backend + frontend teams

**Implementation → Review**
- Backend PR: #123
- Frontend PR: #124
- Tests passing: ✅
- Status: Awaiting review
```

## Escalation

If any agent blocks (e.g., "ambiguous spec"):

1. **Raise issue** in the spec
2. **Tag question** for human review
3. **Pause handoff** until clarified
4. **Update spec** with clarification
5. **Resume** implementation

Example: In spec, mark with `[CLARIFICATION NEEDED]` tag.

---

**Version**: 1.0  
**Last Updated**: February 23, 2026
