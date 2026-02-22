# Agent Selection Guide

How to intelligently select which role-based agents are needed for your feature implementation.

---

## Agent Selection Framework

This framework helps determine which agents to invoke based on your feature requirements.

### Step 1: Analyze Input Type

**What have you provided?**

```
SRS (Complete Requirements)
    ↓
Input involves: User stories, acceptance criteria, diagrams
Action: Run FULL workflow (Project Manager → Architect → Specialized Agents)
Timeline: 10-16 days total

Quick Spec Points (Specific Feature)
    ↓
Input involves: API endpoint, UI component, database table
Action: Skip Project Manager, go straight to Architect + Specialists
Timeline: 5-10 days

Code-level Change (Refactor, Bug, Enhancement)
    ↓
Input involves: Existing code, improvement goal
Action: Skip planning, run Security Expert + Specialist directly
Timeline: 1-3 days

Architecture Decision
    ↓
Input involves: Technology choice, design decision
Action: Run Architect Agent only
Timeline: 1-3 days
```

---

### Step 2: Identify Required Specialties

**What type of work needs to be done?**

Create a checklist:

```
PLANNING WORK:
☐ Do we need requirements breakdown? → Project Manager Agent
☐ Do we need technical architecture? → Architect Agent

IMPLEMENTATION WORK:
☐ Do we need React/frontend code? → Frontend Specialist
☐ Do we need Express/API code? → Backend API Specialist  
☐ Do we need database changes? → Data Analyst
☐ Do we need new API design? → API Designer
☐ Do we need security review? → Security Expert

SUPPORT WORK:
☐ Do we need API documentation? → Documentation Specialist
☐ Do we need final QA approval? → Review/QA Agent
```

---

### Step 3: Determine Execution Sequence

**Which agents run first, second, etc.?**

#### Sequence 1: Full Feature Development

```
1. Project Manager Agent (1-2 days)
   Input: SRS
   Output: Feature breakdown
   ↓
2. Architect Agent (1-3 days)
   Input: Feature breakdown + diagrams
   Output: Tech specs, API contracts, data model
   ↓
3. PARALLEL: Specialized Implementation Agents (3-7 days each)
   ├─ API Designer Agent (prep API contracts) → Backend API Specialist
   ├─ Data Analyst Agent (schema + migrations) → Backend API Specialist
   ├─ Backend API Specialist (implement API)
   └─ Frontend Specialist (implement UI)
   ↓
4. Security Expert Agent (1-3 days) [CAN RUN PARALLEL]
   Input: Implementation code
   Output: Security findings + fixes
   ↓
5. Documentation Specialist Agent (1-2 days) [PARALLEL OK]
   Input: Code + Architecture
   Output: API docs, user guides
   ↓
6. Review/QA Agent (1-2 days)
   Input: All PRs + specs
   Output: APPROVED or CHANGES REQUESTED
```

**Total Timeline**: 10-16 days

#### Sequence 2: Quick API Implementation

```
1. Architect Agent (1-2 days) [SKIP Project Manager - small feature]
   Input: Quick spec + requirements
   Output: API contract, minimal tech decisions
   ↓
2. API Designer Agent (1 day)
   Input: Spec, architecture decision
   Output: OpenAPI spec
   ↓
3. Backend API Specialist Agent (3-5 days)
   Input: API spec, architecture
   Output: Express routes + services
   ↓
4. Data Analyst Agent (2-3 days) [PARALLEL with Backend]
   Input: Data model from Architect
   Output: Migrations, seed scripts
   ↓
5. Security Expert Agent (1-2 days) [CAN PARALLEL]
   Input: Code
   Output: Security review
   ↓
6. Review/QA Agent (1 day)
   Input: PRs + spec
   Output: APPROVED or CHANGES
```

**Total Timeline**: 5-10 days (faster than full feature)

#### Sequence 3: UI-Only Enhancement

```
1. Architect Agent (skip - minimal design needed)
   ↓
2. Frontend Specialist Agent (3-5 days)
   Input: UI spec, mockup, requirements
   Output: React components, tests
   ↓
3. Documentation Specialist Agent (optional, 1 day)
   Input: Component code
   Output: Component storybook docs
   ↓
4. Review/QA Agent (1 day)
   Input: Frontend PR + spec
   Output: APPROVED or CHANGES
```

**Total Timeline**: 4-6 days

#### Sequence 4: Database Work

```
1. Data Analyst Agent (2-3 days)
   Input: Data model from Architect (or existing spec)
   Output: Migrations, indexes, seed data
   ↓
2. Backend API Specialist Agent (3-4 days) [DEPENDS ON DATA ANALYST]
   Input: Migrations from Data Analyst
   Output: Route handlers, queries
   ↓
3. Security Expert Agent (1 day) [PARALLEL with Backend]
   Input: Architecture design
   Output: Data security review
   ↓
4. Review/QA Agent (1 day)
   Input: PR + spec
   Output: APPROVED or CHANGES
```

**Total Timeline**: 6-8 days

---

### Step 4: Agent Selection Decision Tree

**Use this flowchart to pick agents automatically:**

```
START: What are you implementing?

├─ COMPLETE NEW FEATURE FROM SRS
│  ├─ Has user stories? → YES
│  ├─ Has acceptance criteria? → YES
│  ├─ Size: Large (>5 days work)? → YES
│  └─ Agents: PM + Architect + [Specialists] + Security + Docs + QA
│
├─ QUICK API ENDPOINT
│  ├─ Only affects API? → YES
│  ├─ Size: Small (2-3 days)? → YES
│  ├─ Need new tables? → YES/NO
│  └─ Agents: [Skip PM] + Architect + API Designer + Backend + [Data] + Security + QA
│
├─ FRONTEND COMPONENT/PAGE
│  ├─ Only affects UI? → YES
│  ├─ Connects to new API? → YES/NO
│  ├─ Size: Small (2-4 days)? → YES
│  └─ Agents: [Skip PM/Architect] + Frontend + [Backend if API] + [Security] + Docs + QA
│
├─ DATABASE MIGRATION
│  ├─ Only data structure? → YES
│  ├─ Impacts queries? → YES/NO
│  └─ Agents: Data Analyst + [Backend if queries] + [Security] + QA
│
├─ SECURITY AUDIT
│  ├─ Reviewing existing code? → YES
│  ├─ New security feature? → NO
│  ├─ Scope: Specific endpoint/component? → YES
│  └─ Agents: Security Expert → [Specialists if fixes needed]
│
├─ DOCUMENTATION ONLY
│  ├─ Already coded? → YES
│  ├─ Need API docs? → YES/NO
│  └─ Agents: Documentation Specialist + [Review if needed]
│
└─ ARCHITECTURE DECISION
   ├─ Choosing technology? → YES
   ├─ Need implementation guide? → YES/NO
   └─ Agents: [Skip PM] + Architect + [Plan handoff]
```

---

## Feature Type → Agent Mapping

### Authentication & Authorization

**Features**: Login, Register, Password reset, JWT, OAuth, Role-based access

**Required Agents**:
1. ✅ **Project Manager** (scope definition)
2. ✅ **Architect** (security architecture, JWT design)
3. ✅ **API Designer** (auth endpoints, token schema)
4. ✅ **Backend API Specialist** (implement auth, JWT middleware)
5. ✅ **Frontend Specialist** (login form, token storage)
6. ✅ **Security Expert** (auth vulnerabilities, password hashing)
7. ✅ **Documentation Specialist** (how to use auth, JWT examples)
8. ✅ **Review/QA** (verify security, spec compliance)

**Timeline**: 10-14 days

---

### REST API Endpoint

**Features**: GET, POST, PUT, DELETE endpoints, CRUD operations

**Required Agents**:
1. ✅ **Architect** (if new, design; if existing, reference)
2. ✅ **API Designer** (endpoint contract, schema)
3. ✅ **Backend API Specialist** (route, handler, service)
4. ✅ **Data Analyst** (if database affected)
5. ✅ **Security Expert** (input validation, authorization)
6. ✅ **Documentation Specialist** (endpoint docs)
7. ✅ **Review/QA** (verify spec, test coverage)

**Timeline**: 5-8 days

---

### React Component/Page

**Features**: UI components, pages, forms, dashboards

**Required Agents**:
1. ✅ **Frontend Specialist** (build component/page)
2. ⚠️ **Backend API Specialist** (if needs new API)
3. ✅ **Security Expert** (form validation, XSS prevention)
4. ⚠️ **Documentation Specialist** (component docs, optional)
5. ✅ **Review/QA** (accessibility, test coverage)

**Timeline**: 4-8 days

---

### Database Migration

**Features**: New tables, schema changes, indexes, data transformations

**Required Agents**:
1. ✅ **Data Analyst** (migration, indexing)
2. ⚠️ **Backend API Specialist** (if affects queries)
3. ✅ **Security Expert** (data encryption, access control)
4. ✅ **Review/QA** (migration safety, performance)

**Timeline**: 3-5 days

---

### Payment/Subscription Feature

**Features**: Stripe integration, payment processing, billing

**Required Agents**:
1. ✅ **Project Manager** (scope, compliance requirements)
2. ✅ **Architect** (PCI compliance, security architecture)
3. ✅ **API Designer** (payment API contracts)
4. ✅ **Backend API Specialist** (implement payment flow)
5. ✅ **Data Analyst** (transaction tables, reconciliation)
6. ✅ **Frontend Specialist** (payment form, card handling)
7. ✅ **Security Expert** (PCI, encryption, vulnerabilities)
8. ✅ **Documentation Specialist** (refund policy, integration guide)
9. ✅ **Review/QA** (compliance verification)

**Timeline**: 12-18 days (high security requirement)

---

### Public API / Developer Platform

**Features**: API for external developers, webhooks, API keys, rate limiting

**Required Agents**:
1. ✅ **Project Manager** (scope, SLA definitions)
2. ✅ **Architect** (API design, scalability)
3. ✅ **API Designer** (OpenAPI schema, webhook contracts)
4. ✅ **Backend API Specialist** (implement API, rate limiting)
5. ✅ **Data Analyst** (API usage tracking, logging)
6. ✅ **Security Expert** (API key security, DDoS protection)
7. ✅ **Documentation Specialist** (API docs, SDK guides)
8. ✅ **Review/QA** (spec compliance, developer experience)

**Timeline**: 14-20 days (complex, high testing)

---

### Bug Fix / Small Improvement

**Features**: Fix error, improve performance, refactor code

**Can Skip**:  
❌ Project Manager (no new features)
❌ Architect (design already exists)

**Required Agents**:
1. ⚠️ **Specialist** (depends on issue: Frontend for UI bug, Backend for logic bug)
2. ⚠️ **Security Expert** (if security-related)
3. ✅ **Review/QA** (verify fix, test coverage)

**Timeline**: 1-3 days

---

### Documentation Update

**Can Skip**:  
❌ Project Manager
❌ Architect
❌ Implementation Specialists

**Required Agents**:
1. ✅ **Documentation Specialist** (update docs)
2. ⚠️ **Review/QA** (verify accuracy)

**Timeline**: 1-2 days

---

## Agent Execution Rules

### Rule 1: Sequential Dependencies

```
Some agents MUST run before others:

Project Manager → Architect (PM provides requirements breakdown)
Architect → API Designer (Architect defines API contracts)
API Designer → Backend API Specialist (Backend implements to contract)
Data Analyst → Backend API Specialist (Backend uses migrations)
Backend/Frontend → Security Expert (Security audits completed code)
All above → Review/QA (QA verifies everything)
```

### Rule 2: Parallelizable Work

```
These agents can run SIMULTANEOUSLY:

1. Frontend Specialist & Backend API Specialist (parallel implementation)
2. Frontend Specialist & Data Analyst (both work on their domain)
3. API Designer & Data Analyst (both define their contracts)
4. Security Expert can run WHILE other agents still working (audit in parallel)
5. Documentation Specialist can run WHILE feature is being implemented
```

### Rule 3: Optionalness

```
Some agents are OPTIONAL depending on feature:

Optional if no frontend: ❌ Frontend Specialist
Optional if no database: ❌ Data Analyst
Optional if simple query: ❌ API Designer (just use Ad hoc)
Optional for internal APIs: ❌ Documentation Specialist (but recommended)
Optional for low-risk features: ⚠️ Security Expert(included in QA)
```

### Rule 4: Multi-Agent Single Feature

```
SAME FEATURE can use MULTIPLE AGENTS in sequence:

Feature: User Payment
├─ Phase 1: Backend API Specialist (implement payment endpoint)
├─ Phase 2: Security Expert (audit payment code)
├─ Phase 3: Documentation Specialist (payment API docs)
└─ Phase 4: Review/QA (verify everything)

This is NORMAL and ENCOURAGED. Specialized expertise for each aspect.
```

---

## How to Communicate Agent Selection

### Format for Human

**When requesting feature implementation:**

```markdown
# Feature: Payment Processing

SRS: [Link to full requirements]

I need these agents (in this order):
1. Project Manager → Feature breakdown
2. Architect → Technical design, Stripe integration
3. API Designer → Payment API contracts  
4. Backend API Specialist → Implement payment flow
5. Data Analyst → Transaction tables, reconciliation
6. Frontend Specialist → Payment form UI [PARALLEL with backend]
7. Security Expert → PCI compliance audit
8. Documentation Specialist → Payment API docs
9. Review/QA → Final verification

OR just say:
"This is a payment feature" → Copilot auto-selects all needed agents
```

### Format for Auto-Detection

**Copilot intelligent selection:**

```markdown
Feature Type: Payment Processing
Input: Full SRS + ER Diagram + Sequence Diagram  
Size: Large (>10 days work)
Security Level: High

Copilot Auto-Detects & Selects:
✅ Project Manager (required for complex feature)
✅ Architect (define payment architecture)
✅ API Designer (define payment contracts)
✅ Backend API Specialist (implement)
✅ Data Analyst (transactions table)
✅ Frontend Specialist (payment form)
✅ Security Expert (PCI compliance)
✅ Documentation Specialist (API docs)
✅ Review/QA (verification)

Agent sequence already determined from rules.
```

---

## Tips for Effective Agent Selection

✅ **DO**:
- Provide clear feature description
- Include acceptance criteria
- Specify if frontend/backend/database is involved
- Mention security requirements explicitly
- Include timeline constraints

❌ **DON'T**:
- Request agents you don't need (speeds up work)
- Skip Documentation for public APIs (wastes time later)
- Skip Security Expert for auth/payment features (risks compliance)
- Use Project Manager for tiny 1-day features (overkill)

---

## Next Steps

1. **Review Agent Capabilities**: Read [AGENT-REGISTRY.md](AGENT-REGISTRY.md)
2. **Understand Handoffs**: Read [HANDOFF-PROTOCOL.md](HANDOFF-PROTOCOL.md)
3. **Individual Agent Guides**: Read specific agent files
4. **Start Using**: Pick your feature type and let Copilot auto-select agents

---

**Last Updated**: February 23, 2026
