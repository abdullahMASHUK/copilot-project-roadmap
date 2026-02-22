# Role-Based Agent System (NEW)

Complete specialized agent framework with multi-agent workflows for sophisticated feature development.

---

## Overview

Your project now supports a **role-based agent system** where specialized agents with distinct expertise work together sequentially on features. Instead of generic "implementation agents," you can invoke specific specialists:

- **Project Manager** — Requirements breakdown & scope definition
- **Architect** — Technical design & system architecture
- **API Designer** — API contracts & OpenAPI specs
- **Frontend Specialist** — React components & UI
- **Backend API Specialist** — Express routes & business logic
- **Data Analyst** — Database schema & migrations
- **Security Expert** — Vulnerability audit & compliance
- **Documentation Specialist** — API docs & user guides
- **Review/QA** — Quality assurance & final verification

---

## Quick Start

### For One-Off Implementations

**Simple 1-day feature?** Skip Project Manager, go straight to relevant specialists:

```markdown
Feature: Add email field to user profile

Use Agents (in order):
1. Frontend Specialist (build form input)
2. Backend API Specialist (add API endpoint)
3. Data Analyst (migration for new column)
4. Review/QA (verification)

Timeline: 2-3 days
```

### For Complex Features from SRS

**Multi-week feature?** Use full pipeline:

```markdown
Feature: Payment Processing (from SRS)

Use Agents (in order):
1. Project Manager (break down requirements)
2. Architect (design payment flow)
3. API Designer (payment API contracts)
4. Backend API Specialist (implement API)
5. Data Analyst (transaction tables)
6. Frontend Specialist (payment form UI)
7. Security Expert (PCI compliance audit)
8. Documentation Specialist (API docs)
9. Review/QA (final verification)

Timeline: 14-18 days
```

---

## How It Works

### 1. Select Your Agents

**Auto-Selection** (Recommended):
```markdown
Feature: User Dashboard
Input: Full SRS with requirements

Copilot auto-selects based on feature type:
✅ Project Manager (break down requirements)
✅ Architect (design dashboard architecture)
✅ Frontend Specialist (build React pages)
✅ Backend API Specialist (if needs API)
✅ Security Expert (audit sensitive data)
✅ Documentation Specialist (dashboard guide)
✅ Review/QA (verify completeness)
```

**Manual Selection**:
Use [AGENT-SELECTION-GUIDE.md](AGENT-SELECTION-GUIDE.md) to pick agents yourself

### 2. Agents Work Sequentially

Each agent:
1. ✅ Receives input from previous agent (or your SRS)
2. ✅ Does their specialized work
3. ✅ Validates quality before handoff
4. ✅ Hands off to next agent
5. ✅ Provides clear documentation

```
SRS Input
    ↓
Project Manager Agent (scope breakdown)
    ↓
Architect Agent (technical design)
    ↓ [splits to parallel]
├─ API Designer → Backend Specialist
├─ Data Analyst → Backend Specialist
└─ Frontend Specialist
    ↓
Security Expert (audit code)
    ↓
Documentation Specialist (write docs)
    ↓
Review/QA Agent (verify completeness)
    ↓
✅ APPROVED or CHANGES REQUESTED
```

### 3. Work Happens in Parallel Where Possible

Some agents work simultaneously:
- Frontend Specialist & Backend Specialist (parallel implementation)
- API Designer & Data Analyst (both design their contracts)
- Security Expert can audit while others are still building
- Documentation can be written while code is being reviewed

### 4. Pass Quality Validation at Each Step

Every handoff includes quality validation:
- ❌ Does output match expected scope?
- ❌ Are there gaps or errors?  
- ❌ Is output clear for next agent?

If validation fails → Agent revises → Re-submit

---

## Key Documents

| Document | Purpose |
|----------|---------|
| [AGENT-REGISTRY.md](AGENT-REGISTRY.md) | Complete inventory of all agents and their capabilities |
| [AGENT-SELECTION-GUIDE.md](AGENT-SELECTION-GUIDE.md) | How to pick which agents are needed for your feature |
| [HANDOFF-PROTOCOL.md](HANDOFF-PROTOCOL.md) | How agents hand off work to the next agent sequentially |
| [project-manager.agent.md](planning/project-manager.agent.md) | Project Manager Agent specification & responsibilities |
| [architect.agent.md](planning/architect.agent.md) | Architect Agent specification (technical design) |
| [Other agent files](implementation/) | Individual agent specifications |

---

## Real Example: Payment Feature

**Scenario**: You want to build a payment processing feature with credit card handling.

### Step 1: Understand Your Need

```
Feature Type: Payments (high security, multi-component)
Complexity: High (14-18 days)
Input Type: Full SRS
Requirements: PCI compliance, security audit, full documentation needed
```

### Step 2: Select Agents (Auto-Select)

Copilot analyzes and auto-selects:
```
🎯 Payment Feature Auto-Selected Agents:
1. ✅ Project Manager (scope: PCI compliance, data security)
2. ✅ Architect (design: Stripe integration, data flow, security measures)
3. ✅ API Designer (contract: Payment endpoints, webhook handling)
4. ✅ Backend API Specialist (implement: Express payment routes)
5. ✅ Data Analyst (schema: Transactions, reconciliation, PCI)
6. ✅ Frontend Specialist (UI: Payment form, error handling)
7. ✅ Security Expert (audit: PCI compliance, encryption, vulnerabilities)
8. ✅ Documentation Specialist (docs: Payment API, integration guide)
9. ✅ Review/QA (verify: All criteria met, security approved)
```

### Step 3: Execute Pipeline

```
Day 1-2: Project Manager
  Output: Feature breakdown with
    - Payments acceptance
    - Refunds handling
    - Dispute resolution
  
  Scope: In: Payment processing, refunds
         Out: Subscription management, invoicing
  
  Timeline: 14-18 days
         
         ↓

Day 3-5: Architect
  Output: Payment architecture with
    - Stripe integration design
    - PCI compliance architecture
    - Data security measures
  
  Passes specialized guides to:
    - API Designer (payment API contracts)
    - Backend Specialist (payment flow)
    - Data Analyst (transaction schema)
         
         ↓

Day 6-7: API Designer (parallel)
  Output: Stripe webhook contracts
          OpenAPI spec: /api/payments/*
          
         ↓ To Backend Specialist

Day 8-10: Backend Specialist + Data Analyst (parallel)
  Backend implements:  /api/payments POST (charge)
                       /api/payments/:id/refund
                       /api/webhooks/stripe
  
  Data Analyst creates: migrations (transactions, audit log)
                        reconciliation queries
                        
         ↓

Day 11-13: Frontend + Partial Security (parallel)
  Frontend implements: Payment form
                       Card validation
                       Error display
  
  Security audits: Starts audit on backend code
                   Checks encryption, PCI compliance
         
         ↓

Day 14: Full Security Expert Review
  Output: Security findings and fixes
          PCI compliance verification
          Encryption verification
  
  All code must pass security before QA
         
         ↓

Day 15: Documentation Specialist (while waiting for QA)
  Output: Payment API documentation
          Integration guide for customers
          Refund policy documentation
          Webhook examples
         
         ↓

Day 16: Review/QA Agent
  Verifies:
    ✅ All payment flows working
    ✅ All acceptance criteria met
    ✅ Error handling correct
    ✅ Refunds working
    ✅ Security approved
    ✅ Compliance verified
    ✅ Documentation complete
  
  Output: APPROVED ✅
         
         ↓

Day 17: [Merge & Deploy]
```

**Result**: 
- Full payment system with:
  - 3 agents working simultaneously (parallel)
  - 2 quality validations (architecture → code, security → QA)
  - Complete security audit
  - Full documentation
  - PCI compliance verified
  - Ready for production

---

## When to Use This System

### ✅ Use Role-Based Agents When:

- 🎯 Building from complete SRS
- 🎯 Feature has security requirements
- 🎯 Multiple teams will implement (use role specialists)
- 🎯 Public API or integration
- 🎯 Complex data model or transactions
- 🎯 Need security audit
- 🎯 Need comprehensive documentation
- 🎯 Timeline is flexible (can do 10-16 days)

### ⚠️ Use Simplified Orchestration When:

- ⏱️ Quick feature (<2 days)
- ⏱️ Straightforward implementation
- ⏱️ Low security risk
- ⏱️ Simple UI/API without database
- ⏱️ No documentation needed
- ⏱️ Timeline critical

---

## Differences from Previous Orchestration

| Aspect | Old Orchestration | New Role-Based |
|--------|------------------|-----------------|
| **Planner** | 1 generic Project Planner | 2 specialists: Project Manager + Architect |
| **Implementers** | Backend + Frontend agents | 7 specialists by role (API Designer, Backend API, Frontend, Data, Security, Docs) |
| **Selection** | All-or-nothing | Pick exactly which agents needed |
| **Flexibility** | Fixed workflow | Flexible sequential composition |
| **Parallelization** | Frontend + Backend only | Multiple roles can work simultaneously |
| **Specialization** | Generic | Highly specialized role-based |
| **Documentation** | Single doc agent | Integrated throughout |
| **Security** | Part of QA | Dedicated security specialist |

---

## File Structure

```
.github/agents/roles/
├── README.md                    ← You are here
├── AGENT-REGISTRY.md            ← All agents & their capabilities
├── AGENT-SELECTION-GUIDE.md     ← How to pick agents
├── HANDOFF-PROTOCOL.md          ← Sequential execution rules
│
├── planning/
│   ├── project-manager.agent.md (SRS → feature breakdown)
│   └── architect.agent.md       (requirements → technical design)
│
├── implementation/
│   ├── frontend-specialist.agent.md
│   ├── backend-api-specialist.agent.md
│   ├── data-analyst.agent.md
│   ├── api-designer.agent.md
│   └── security-expert.agent.md
│
└── support/
    ├── documentation-specialist.agent.md
    └── review-qa.agent.md (from orchestration/)
```

---

## Getting Started

### Step 1: Read the Overview Documents

1. Start here: [AGENT-REGISTRY.md](AGENT-REGISTRY.md) — See all available agents
2. Next: [AGENT-SELECTION-GUIDE.md](AGENT-SELECTION-GUIDE.md) — Pick agents for your feature
3. Then: [HANDOFF-PROTOCOL.md](HANDOFF-PROTOCOL.md) — Understand how they work together

### Step 2: Choose Your Feature Type

Look up your feature in [AGENT-SELECTION-GUIDE.md](AGENT-SELECTION-GUIDE.md#feature-type--agent-mapping):
- Authentication? → Use these agents
- Payment? → Use these agents
- Dashboard? → Use these agents
- API endpoint? → Use these agents

### Step 3: Communicate with Copilot

```markdown
I have a new feature to implement.

Feature: [Name]
Type: [Category from guide]
Input: [SRS / Quick Spec / Code]

[Your requirements]

Please:
1. Auto-select appropriate agents
2. Execute them in sequence
3. Hand off between agents
4. Provide complete output
```

### Step 4: Monitor Progress

Agents will report at each handoff:
```
✅ Project Manager complete → Handing to Architect
✅ Architect complete → Handing to [Specialists]
✅ Backend Specialist complete → Handing to Security Expert
[Continue...]
✅ All agents complete → Review/QA verification in progress
```

---

## FAQ

**Q: Do I always need Project Manager?**  
A: No. Only for complex features from full SRS. Skip for small features.

**Q: Can agents run in parallel?**  
A: Yes! But some have dependencies. HANDOFF-PROTOCOL.md explains which can run together.

**Q: What if a feature needs custom roles?**  
A: Create a new agent following the same template. All agents follow the same interface.

**Q: How long does a feature take?**  
A: Depends on complexity. See AGENT-SELECTION-GUIDE.md for timelines per feature type.

**Q: Can I skip agents?**  
A: Yes, if not needed for your feature. But don't skip Security Expert for sensitive features.

**Q: What if agents disagree on approach?**  
A: HANDOFF-PROTOCOL.md has escalation rules. Rare, but handled.

---

## Next Steps

1. **Explore Agents**: Read [AGENT-REGISTRY.md](AGENT-REGISTRY.md)
2. **Pick Your Agents**: Use [AGENT-SELECTION-GUIDE.md](AGENT-SELECTION-GUIDE.md)
3. **Understand Handoffs**: Read [HANDOFF-PROTOCOL.md](HANDOFF-PROTOCOL.md)
4. **Start Building**: Tell Copilot your feature and let it auto-select agents

---

**System Version**: 1.0 (Role-Based Agents)  
**Date Launched**: February 23, 2026  
**Status**: Ready for production use

**Related**: [.github/agents/orchestration/](../orchestration/) (simplified orchestration for quick features)
