# Role-Based Agent Registry

Complete inventory of all specialized agents in your organization. This registry enables intelligent agent selection and sequential execution.

---

## Agent Registry Overview

All agents are organized by **phase** and **specialty**. Within each phase, agents work sequentially to provide specialized expertise.

```
┌─────────────────────────────────────────────────────────┐
│ PLANNING PHASE: SRS → Specifications & Designs         │
├─────────────────────────────────────────────────────────┤
│ 1. Project Manager Agent  (requirements breakdown)      │
│ 2. Architect Agent        (technical design)            │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ IMPLEMENTATION PHASE: Specs → Code (based on need)      │
├─────────────────────────────────────────────────────────┤
│ • Frontend Specialist     (React, UI/UX)                │
│ • Backend API Specialist  (Express, services)           │
│ • Data Analyst           (database, migrations)         │
│ • API Designer           (API contracts, schemas)       │
│ • Security Expert        (audit, best practices)        │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│ SUPPORT PHASE: Documentation & Review                   │
├─────────────────────────────────────────────────────────┤
│ • Documentation Specialist (guides, diagrams)           │
│ • Review/QA Agent         (spec compliance, quality)    │
└─────────────────────────────────────────────────────────┘
```

---

## Planning Phase Agents

### 1. Project Manager Agent

**Specialty**: Requirements Analysis & Scope Definition  
**Priority**: FIRST (runs before Architect)

| Property | Value |
|----------|-------|
| **File** | [`.github/agents/roles/planning/project-manager.agent.md`](.github/agents/roles/planning/project-manager.agent.md) |
| **Input** | SRS with user stories, requirements, acceptance criteria |
| **Output** | Requirements breakdown, feature list, dependencies, timeline estimate, scope document |
| **Timeline** | 1-2 days |
| **Triggers When** | User provides SRS with complete requirements |
| **Hands Off To** | Architect Agent (provides scope to architect) |

**Responsibilities**:
- ✅ Parse SRS and extract requirements
- ✅ Break down into features with dependencies
- ✅ Create feature breakdown document
- ✅ Estimate timeline for each feature
- ✅ Identify scope boundaries
- ✅ Document assumptions and constraints
- ✅ Create acceptance criteria clarity document

**Sample Output**:
```
Feature Breakdown:
├─ Feature 1: User Authentication (3-4 days)
│  ├─ Login endpoint
│  ├─ JWT tokens
│  └─ Password reset flow
├─ Feature 2: User Dashboard (4-5 days)
│  ├─ Profile display
│  ├─ Analytics summary
│  └─ Settings page
└─ Feature 3: Admin Panel (3-4 days)
   ├─ User management
   ├─ Reports
   └─ System settings

Timeline: 10-13 days total
Dependencies: Authentication must complete before Dashboard
```

---

### 2. Architect Agent

**Specialty**: Technical Design & System Architecture  
**Priority**: SECOND (runs after Project Manager)

| Property | Value |
|----------|-------|
| **File** | [`.github/agents/roles/planning/architect.agent.md`](.github/agents/roles/planning/architect.agent.md) |
| **Input** | Feature breakdown from Project Manager + ER Diagram + Sequence diagrams |
| **Output** | Technical architecture, data model, API contracts, system design diagrams, tech stack decisions |
| **Timeline** | 1-3 days |
| **Triggers When** | Project Manager completes requirements breakdown |
| **Hands Off To** | Implementation Specialists (provides tech specs) |

**Responsibilities**:
- ✅ Design data model and database schema
- ✅ Define API contracts and endpoints
- ✅ Create system architecture diagrams
- ✅ Make tech stack decisions for each feature
- ✅ Design integration points
- ✅ Identify performance considerations
- ✅ Plan security architecture
- ✅ Create implementation guides per role

**Sample Output**:
```
Technical Architecture:

Data Model:
- User (id, email, password_hash, profile_data)
- Post (id, user_id, title, content, created_at)
- Comment (id, post_id, user_id, text, created_at)

API Contracts:
POST /api/auth/login
  Request: { email, password }
  Response: { token, user }
  Errors: 401, 400

Frontend Architecture:
- React Context for auth state
- Custom hooks for API calls
- Tailwind CSS for styling

Backend Architecture:
- Express 4 with services layer
- JWT middleware for auth
- PostgreSQL for data storage
```

---

## Implementation Phase Agents

### 3. Frontend Specialist Agent

**Specialty**: React Components, UI/UX Implementation  
**Priority**: Parallel with Backend specialists

| Property | Value |
|----------|-------|
| **File** | [`.github/agents/roles/implementation/frontend-specialist.agent.md`](.github/agents/roles/implementation/frontend-specialist.agent.md) |
| **Input** | Frontend implementation guide from Architect |
| **Output** | React components, pages, routing, state management, tests (>75% coverage), accessibility verified |
| **Timeline** | 3-7 days per feature |
| **Triggers When** | Architecture phase complete AND frontend work needed |
| **Hands Off To** | Review/QA Agent OR Documentation Agent |

**Responsibilities**:
- ✅ Build React components following patterns
- ✅ Implement pages and routing
- ✅ Set up state management (Context API)
- ✅ Integrate with backend APIs
- ✅ Implement form validation
- ✅ Create responsive design
- ✅ Ensure accessibility (WCAG 2.1 AA)
- ✅ Write tests (>75% coverage)
- ✅ Performance optimization

**When to Use**: User authentication, dashboards, forms, user-facing features

---

### 4. Backend API Specialist Agent

**Specialty**: Express APIs, Route Handlers, Business Logic  
**Priority**: Parallel with Frontend specialist

| Property | Value |
|----------|-------|
| **File** | [`.github/agents/roles/implementation/backend-api-specialist.agent.md`](.github/agents/roles/implementation/backend-api-specialist.agent.md) |
| **Input** | API contracts and backend implementation guide from Architect |
| **Output** | Express routes, handlers, services, input validation, error handling, tests (>80% coverage) |
| **Timeline** | 3-7 days per feature |
| **Triggers When** | Architecture phase complete AND API implementation needed |
| **Hands Off To** | Data Analyst (migrations) OR Security Expert (security layer) |

**Responsibilities**:
- ✅ Create Express routes and handlers
- ✅ Implement business logic services
- ✅ Add input validation
- ✅ Implement error handling
- ✅ Add logging and monitoring hooks
- ✅ Write tests (>80% coverage)
- ✅ Document API behavior
- ✅ Performance optimization

**When to Use**: API endpoints, services, business logic implementation

---

### 5. Data Analyst Agent

**Specialty**: Database Design, Migrations, Data Pipelines  
**Priority**: Often parallel, sometimes sequential

| Property | Value |
|----------|-------|
| **File** | [`.github/agents/roles/implementation/data-analyst.agent.md`](.github/agents/roles/implementation/data-analyst.agent.md) |
| **Input** | Data model and schema from Architect |
| **Output** | Database migrations, seed scripts, queries, performance indexes, data validation rules |
| **Timeline** | 2-4 days per feature |
| **Triggers When** | Database changes needed |
| **Hands Off To** | Backend API Specialist (uses migrations) |

**Responsibilities**:
- ✅ Create database migrations (Knex/Sequelize)
- ✅ Create seed scripts for testing
- ✅ Optimize queries and add indexes
- ✅ Implement data validations
- ✅ Create database diagrams
- ✅ Plan data backup/restore
- ✅ Performance testing

**When to Use**: Any feature with database changes, data transformations, migrations

---

### 6. API Designer Agent

**Specialty**: API Contracts, RESTful Design, GraphQL, OpenAPI Specs  
**Priority**: Sometimes before Backend, sometimes parallel

| Property | Value |
|----------|-------|
| **File** | [`.github/agents/roles/implementation/api-designer.agent.md`](.github/agents/roles/implementation/api-designer.agent.md) |
| **Input** | Feature requirements and data model |
| **Output** | OpenAPI/Swagger specs, endpoint contracts, error responses, versioning strategy |
| **Timeline** | 1-2 days |
| **Triggers When** | New API design needed OR complex API contracts |
| **Hands Off To** | Backend API Specialist (implements from contracts) |

**Responsibilities**:
- ✅ Design REST API endpoints
- ✅ Create OpenAPI/Swagger specs
- ✅ Define error response schemas
- ✅ Plan API versioning
- ✅ Document request/response examples
- ✅ Consider pagination, filtering
- ✅ Design webhook contracts if needed

**When to Use**: New API, API redesign, complex integrations, multi-consumer APIs

---

### 7. Security Expert Agent

**Specialty**: Security Audit, Vulnerabilities, Best Practices  
**Priority**: Can run parallel or after implementation

| Property | Value |
|----------|-------|
| **File** | [`.github/agents/roles/implementation/security-expert.agent.md`](.github/agents/roles/implementation/security-expert.agent.md) |
| **Input** | Code (Backend/Frontend/API) or architecture design |
| **Output** | Security findings, recommendations, fixes, security tests |
| **Timeline** | 1-3 days |
| **Triggers When** | Code complete AND security review needed |
| **Hands Off To** | Review/QA Agent |

**Responsibilities**:
- ✅ Identify vulnerabilities
- ✅ Check OWASP top 10
- ✅ Review authentication/authorization
- ✅ Check input validation
- ✅ Review SQL injection risks
- ✅ Check API security
- ✅ Verify encryption usage
- ✅ Create security tests
- ✅ Document security recommendations

**When to Use**: Authentication, payment processing, sensitive data, public APIs, new features

---

## Support Phase Agents

### 8. Documentation Specialist Agent

**Specialty**: API Docs, User Guides, Architecture Docs  
**Priority**: Can run parallel with implementation or after

| Property | Value |
|----------|-------|
| **File** | [`.github/agents/roles/support/documentation-specialist.agent.md`](.github/agents/roles/support/documentation-specialist.agent.md) |
| **Input** | Architecture, code, implementation guides |
| **Output** | API documentation, user guides, setup guides, integration examples |
| **Timeline** | 1-2 days |
| **Triggers When** | Feature complete OR documentation needed |
| **Hands Off To** | Review/QA Agent |

**Responsibilities**:
- ✅ Create API documentation
- ✅ Write user guides
- ✅ Create setup/deployment guides
- ✅ Write integration examples
- ✅ Create architecture diagrams
- ✅ Update README files
- ✅ Create troubleshooting guides
- ✅ Update CHANGELOG

**When to Use**: After feature complete, before release

---

### 9. Review/QA Agent

**Specialty**: Quality Assurance, Compliance, Acceptance Testing  
**Priority**: LAST (after all implementation)

| Property | Value |
|----------|-------|
| **File** | [`.github/agents/orchestration/review-qa.agent.md`](.github/agents/orchestration/review-qa.agent.md) |
| **Input** | All code PRs + specifications |
| **Output** | APPROVED or CHANGES REQUESTED with detailed feedback |
| **Timeline** | 1-2 days |
| **Triggers When** | All implementation complete |
| **Hands Off To** | Merge & Deploy (if approved) |

**Responsibilities**:
- ✅ Verify spec compliance
- ✅ Check acceptance criteria
- ✅ Verify test coverage (backend >80%, frontend >75%)
- ✅ Code quality review
- ✅ Security verification
- ✅ Accessibility audit (frontend)
- ✅ Performance check
- ✅ Document impact assessment

**When to Use**: Final quality gate before production

---

## Quick Reference: Agent Selection Guide

### By Feature Type

| Feature Type | Required Agents | Timeline |
|--------------|-----------------|----------|
| **Auth/Login** | Backend API, Frontend, Security, API Designer | 7-10 days |
| **CRUD API** | API Designer, Backend API, Data Analyst | 5-7 days |
| **Dashboard/UI** | Frontend, Backend API (if needed) | 5-8 days |
| **Data Migration** | Data Analyst, Backend API | 3-5 days |
| **Integration** | API Designer, Backend API, Security | 7-10 days |
| **Public API** | API Designer, Backend API, Security, Documentation | 10-14 days |
| **Payment/Sensitive** | Backend API, Security Expert, Data Analyst | 10-15 days |

### By Phase Sequence

```
PLANNING:
Project Manager Agent ──→ Architect Agent
         (1-2 days)           (1-3 days)

IMPLEMENTATION (Parallel/Sequential based on need):
├─ API Designer (1-2 days) ──→ Backend API Specialist (3-7 days)
├─ Data Analyst (2-4 days) ──→ Backend API Specialist (if not done)
├─ Frontend Specialist (3-7 days) ──→ (parallel with backend)
├─ Security Expert (1-3 days) ──→ (can run on completed code)
└─ Documentation Specialist (1-2 days) ──→ (parallel or after)

SUPPORT:
Review/QA Agent (1-2 days) ──→ APPROVED / CHANGES REQUESTED
```

---

## Agent Roles Summary Table

| Agent | Phase | Specialty | Input | Output | Timeline |
|-------|-------|-----------|-------|--------|----------|
| **Project Manager** | Planning | Requirements | SRS | Feature breakdown, scope | 1-2d |
| **Architect** | Planning | Design | Breakdown + diagrams | Tech specs, API contracts | 1-3d |
| **Frontend Specialist** | Implementation | React, UI | Frontend guide | Components, tests | 3-7d |
| **Backend API Specialist** | Implementation | Express, Logic | API guide | Routes, services, tests | 3-7d |
| **Data Analyst** | Implementation | Database | Schema | Migrations, queries | 2-4d |
| **API Designer** | Implementation | API Design | Requirements | OpenAPI specs | 1-2d |
| **Security Expert** | Implementation | Security | Code/Design | Findings, recommendations | 1-3d |
| **Documentation Specialist** | Support | Docs | Code, Architecture | API docs, guides | 1-2d |
| **Review/QA** | Support | Quality | All PRs + specs | APPROVED / CHANGES | 1-2d |

---

## File Structure

```
.github/agents/roles/
├── AGENT-REGISTRY.md               ← You are here
├── AGENT-SELECTION-GUIDE.md        ← How to pick agents (next)
├── HANDOFF-PROTOCOL.md             ← Sequential execution rules
│
├── planning/
│   ├── project-manager.agent.md
│   └── architect.agent.md
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
    └── review-qa.agent.md
```

---

## How Copilot Uses This Registry

When you submit SRS/specifications:

1. **Auto-detect phase**: Copilot reads your input
2. **Identify needed roles**: Based on feature type, complexity, requirements
3. **Check dependencies**: Determine sequential vs parallel execution  
4. **Execute agents**: Run selected agents in correct order
5. **Handoff between agents**: Pass outputs as inputs to next agent
6. **Aggregate results**: Combine all agent outputs into implementation plan

**Example**: "New payment feature"
```
Copilot analyzes → Identifies:
├─ Project Manager (scope breakdown)
├─ Architect (payment security, data model)
├─ API Designer (payment API contracts)
├─ Backend API Specialist (implement payment endpoints)
├─ Data Analyst (transactions table, reconciliation queries)
├─ Security Expert (PCI compliance, encryption)
├─ Documentation Specialist (payment API docs)
└─ Review/QA (verify compliance + security)
```

Each hands off to the next, building upon previous work.

---

**Start Here**: [AGENT-SELECTION-GUIDE.md](AGENT-SELECTION-GUIDE.md)

**Last Updated**: February 23, 2026
