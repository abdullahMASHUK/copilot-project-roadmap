# Orchestration Workflow Guide

This guide walks through the complete workflow from SRS to implementation to review. It's the practical "how-to" for using the agent orchestration system.

---

## Quick Start

### Step 1: Prepare SRS (5-10 business days)

**Who**: Product Owner, Requirements Analyst

**What**: Write Software Requirements Specification

**Template**: [srs-input-template.md](./srs-input-template.md)

**Output**: SRS document at `docs/projects/{project}/srs/{feature}/SRS.md`

**Checklist**:
- [ ] Problem clearly stated
- [ ] User stories written
- [ ] Functional requirements listed
- [ ] Non-functional requirements specified (performance, security, etc.)
- [ ] Data entities/relationships defined
- [ ] Constraints and risks documented
- [ ] Acceptance criteria defined
- [ ] Stakeholders have reviewed and approved

---

### Step 2: Planning (5-10 business days)

**Who**: Project Planner Agent + Tech Lead

**What**: Transform SRS into actionable specifications, diagrams, and implementation guides

**Input to Planner**:
```
I need to plan this feature for [project].

SRS: [docs/projects/{project}/srs/{feature}/SRS.md](link)
Project Context: [docs/projects/{project}/context.md](link)
Domain Context: [docs/domains/{backend|frontend}/context.md](link)

Analyze the SRS thoroughly and produce:

1. Feature Specification with acceptance criteria
2. ER diagram(s) for database schema
3. Sequence diagrams for critical workflows
4. Backend Implementation Guide (API endpoints, business logic, DB schema)
5. Frontend Implementation Guide (pages, components, state management, API contract)
6. Identify any ambiguities, risks, or dependencies
7. Flag any missing requirements

Reference: [.github/agents/orchestration/project-planner.agent.md](../agents/orchestration/project-planner.agent.md)
```

**Output Locations**:
```
docs/projects/{project}/features/{feature}/
├── spec.md                    (feature specification + acceptance criteria)
├── backend-guide.md           (API endpoints, business logic, schemas)
├── frontend-guide.md          (pages, components, state management)
├── diagrams/
│   ├── er.md                  (entity-relationship diagram)
│   └── sequences.md           (sequence diagrams for critical flows)
└── [OPTIONAL] context.md      (feature-specific context)
```

**Approval**:
- [ ] Tech lead reviews specs
- [ ] Product owner approves acceptance criteria
- [ ] Architecture aligns with project decisions
- [ ] No blocking ambiguities remain

---

### Step 3: Implementation (Parallel)

Implementation happens in parallel. Backend and frontend teams work simultaneously.

#### A. Backend Implementation (7-15 business days)

**Who**: Backend Implementation Agent + Backend Developers

**Handler**: [.github/agents/orchestration/backend-implementation.agent.md](../agents/orchestration/backend-implementation.agent.md)

**Input**:
```
I'm implementing [feature] backend according to this spec.

Spec: [docs/projects/backend-api/features/{feature}/spec.md]
Backend Guide: [docs/projects/backend-api/features/{feature}/backend-guide.md]
ER Diagram: [docs/projects/backend-api/features/{feature}/diagrams/er.md]

Reference:
- Project Context: [docs/projects/backend-api/context.md]
- Project Memory: [docs/projects/backend-api/memory.md]
- Backend Instructions: [docs/domains/backend/context.md]
- Path Instructions: [.github/instructions/paths/]

Tech Stack:
- Framework: [from context]
- Database: [from context]
- Testing: [from context]

Build the following:
1. Database migrations (if needed)
2. Data models/schemas
3. Service layer (business logic)
4. API handlers
5. Input validation
6. Error handling
7. Tests (unit + integration)

Acceptance Criteria: [5-10 from spec]
```

**Output**:
- Pull Request with code
- All tests passing (coverage > 80%)
- API documentation updated
- No breaking changes (or migration provided)

**Verification**:
- [ ] Code follows project patterns
- [ ] All acceptance criteria have tests
- [ ] Tests passing locally and in CI
- [ ] Performance targets met

**Handoff Checklist**:
- [ ] Backend PR created
- [ ] Tests passing
- [ ] CI checks green
- [ ] Coverage reported
- [ ] No merge conflicts
- [ ] Ready for review

---

#### B. Frontend Implementation (7-15 business days)

**Who**: Frontend Implementation Agent + Frontend Developers

**Handler**: [.github/agents/orchestration/frontend-implementation.agent.md](../agents/orchestration/frontend-implementation.agent.md)

**Input**:
```
I'm implementing [feature] frontend according to this spec.

Spec: [docs/projects/frontend-web/features/{feature}/spec.md]
Frontend Guide: [docs/projects/frontend-web/features/{feature}/frontend-guide.md]

Reference:
- Project Context: [docs/projects/frontend-web/context.md]
- Project Memory: [docs/projects/frontend-web/memory.md]
- Frontend Instructions: [docs/domains/frontend/context.md]
- Path Instructions: [.github/instructions/paths/components.instructions.md]

Tech Stack:
- Framework: [from context]
- State Management: [from context]
- Testing: [from context]

Build the following:
1. Page components
2. Reusable components
3. Custom hooks (data fetching)
4. API integration
5. State management
6. Styling (responsive, accessible)
7. Tests (component + integration)

Acceptance Criteria: [5-10 from spec]
```

**Output**:
- Pull Request with code
- All tests passing (coverage > 75%)
- Responsive design verified
- Accessibility verified (WCAG AA)
- Lighthouse score > 90

**Verification**:
- [ ] Code follows project patterns
- [ ] All acceptance criteria have tests
- [ ] Tests passing locally and in CI
- [ ] Responsive design works (mobile/tablet/desktop)
- [ ] Accessibility verified (WCAG AA)
- [ ] Performance good (Lighthouse)

**Handoff Checklist**:
- [ ] Frontend PR created
- [ ] Tests passing
- [ ] CI checks green
- [ ] Coverage reported
- [ ] Lighthouse score reported
- [ ] Responsive design verified
- [ ] Accessibility verified
- [ ] No merge conflicts
- [ ] Ready for review

---

### Step 4: Code Review & QA (3-5 business days)

**Who**: Review/QA Agent + Code Reviewers

**Handler**: [.github/agents/orchestration/review-qa.agent.md](../agents/orchestration/review-qa.agent.md)

**Input** (After both implementations ready):
```
Please review these implementations against the specification.

Specifications:
- Feature Spec: [docs/projects/{project}/features/{feature}/spec.md]
- Acceptance Criteria: [5-10 criteria]
- Original SRS: [docs/projects/{project}/srs/{feature}/SRS.md]

Backend Implementation:
- PR: [Link to backend PR]
- Status: Tests passing, CI green

Frontend Implementation:
- PR: [Link to frontend PR]
- Status: Tests passing, CI green

Verify:
1. All spec requirements implemented
2. All acceptance criteria passing
3. Code quality acceptable
4. Test coverage adequate
5. Security reviewed
6. Performance verified
7. End-to-end workflows work
8. Integration between frontend and backend correct

Reference: [.github/agents/orchestration/review-qa.agent.md](../agents/orchestration/review-qa.agent.md)
```

**Review Checklist**:
- [ ] **Specification Compliance**: Every requirement implemented, every criterion met
- [ ] **Code Quality**: Follows patterns, no code smells, error handling consistent
- [ ] **Testing**: Coverage adequate, edge cases tested, all tests passing
- [ ] **Security**: No vulnerabilities, input validated, secrets protected
- [ ] **Performance**: Targets met, queries optimized, no N+1s, Lighthouse good
- [ ] **Accessibility**: WCAG AA (if frontend), keyboard nav, screen readers
- [ ] **Integration**: Frontend ↔ Backend working correctly, error handling
- [ ] **Documentation**: Updated, commented, recorded learnings

**Output** (One of):

**If APPROVED** ✅:
```markdown
# Review Complete: Feature Approved

Status: READY TO MERGE

This feature meets all specifications and quality standards.

Backend:
✅ Specification implemented
✅ Tests passing (85% coverage)
✅ Performance targets met
✅ Security reviewed

Frontend:
✅ Specification implemented  
✅ Tests passing (82% coverage)
✅ Responsive design verified
✅ Accessibility verified (WCAG AA)
✅ Performance good (Lighthouse 94)

Next Steps: Human approval and merge to main
```

**If CHANGES REQUESTED** ⚠️:
```markdown
# Review Complete: Changes Requested

Status: NEEDS FIXES

The following issues must be addressed:

Backend Issues:
- [ ] Issue 1: [Description and how to fix]
- [ ] Issue 2: [Description and how to fix]

Frontend Issues:
- [ ] Issue 1: [Description and how to fix]

Timeline to fix: [Hours/Days]

After fixes, resubmit both PRs.
```

---

### Step 5: Merge & Deploy (1-2 business days)

**Who**: Tech Lead, DevOps

**Manual Steps**:
- [ ] Human approves review
- [ ] Backend PR merged to main
- [ ] Frontend PR merged to main
- [ ] CI/CD pipeline runs
- [ ] Automated tests run
- [ ] Deployed to staging
- [ ] Smoke tests pass
- [ ] Deployed to production
- [ ] Monitoring enabled
- [ ] Team notified

---

## Orchestration Workflow Diagram

```
┌─────────────────────────────────────────────────────────────┐
│  PHASE 1: REQUIREMENTS (5-10 days)                         │
│  Product Owner writes SRS                                  │
│  Result: docs/projects/{project}/srs/{feature}/SRS.md     │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ↓
┌─────────────────────────────────────────────────────────────┐
│  PHASE 2: PLANNING (5-10 days)                             │
│  Project Planner Agent transforms SRS → Specs              │
│  Results:                                                  │
│  - docs/projects/{project}/features/{feature}/spec.md     │
│  - backend-guide.md                                        │
│  - frontend-guide.md                                       │
│  - ER diagrams                                             │
│  - Sequence diagrams                                       │
└────────────────────┬────────────────────────────────────────┘
                     │
          ┌──────────┴──────────┐
          │                     │
          ↓                     ↓
    ┌─────────────┐       ┌─────────────┐
    │  PHASE 3A:  │       │  PHASE 3B:  │
    │  BACKEND    │       │  FRONTEND   │
    │  (7-15 days)│       │  (7-15 days)│
    │             │       │             │
    │ Backend     │       │ Frontend    │
    │ Impl Agent  │       │ Impl Agent  │
    │             │       │             │
    │ Output:     │       │ Output:     │
    │ Backend PR  │       │ Frontend PR │
    └──────┬──────┘       └──────┬──────┘
           │                     │
           │   (PARALLEL)        │
           │                     │
           └──────────┬──────────┘
                      ↓
┌─────────────────────────────────────────────────────────────┐
│  PHASE 4: REVIEW (3-5 days)                                │
│  Review/QA Agent verifies both implementations              │
│  Result: APPROVED or CHANGES REQUESTED                     │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ↓
┌─────────────────────────────────────────────────────────────┐
│  PHASE 5: DEPLOYMENT (1-2 days)                            │
│  Human approval → Merge → CI/CD → Deploy                   │
│  Result: Feature in production                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Example: Complete Workflow

### Feature: User Dashboard

**Timeline**: 4 weeks total

**Week 1: SRS (5 days)**
- Product owner writes SRS for user dashboard
- Covers: user roles, data display, filtering, sorting, refresh
- Team reviews and approves
- Output: `docs/projects/frontend-web/srs/dashboard/SRS.md`

**Week 2: Planning (5 days)**
- Project Planner Agent analyzes SRS
- Creates feature spec with 8 acceptance criteria
- Diagrams: ER (if DB changes), sequence (user sees data)
- Guides: Backend (what API endpoints needed), Frontend (pages/components)
- Output: `docs/projects/frontend-web/features/dashboard/`

**Week 3: Implementation (7 days, parallel)**
- Backend team: Implements `/api/v1/dashboard` endpoint, tests, PR ready
- Frontend team: Builds Dashboard page, components, state, tests, PR ready
- Both teams ready for review by end of week

**Week 4: Review & Deploy (5 days)**
- Review Agent verifies:
  - ✅ 8 acceptance criteria met
  - ✅ Backend spec correct (tests pass, coverage 85%)
  - ✅ Frontend spec correct (tests pass, coverage 80%, Lighthouse 92)
  - ✅ Integration working (frontend calls backend APIs)
  - ✅ Performance targets met
  - ✅ Accessibility verified (WCAG AA)
- Day 4: Human approves
- Day 5: Merged to main, deployed to production

---

## Team Roles & Responsibilities

| Role | Responsibilities | When Active |
|------|-----------------|-------------|
| **Product Owner** | Write SRS, approve specs, define acceptance criteria | Week 1 |
| **Tech Lead** | Review plans, guide architecture, approve implementations | Week 2, 4 |
| **Project Planner Agent** | Transform SRS → specs, create diagrams, write guides | Week 2 |
| **Backend Dev + Agent** | Implement backend, write tests, create PR | Week 3 |
| **Frontend Dev + Agent** | Implement frontend, write tests, create PR | Week 3 |
| **Review/QA Agent** | Verify implementations against specs | Week 4 (early) |
| **Code Reviewers** | Manual review of code quality, security, patterns | Week 4 (early) |
| **DevOps** | Deploy to production, monitoring | Week 4 (late) |

---

## Handoff Gates (Quality Checkpoints)

**Before Planner → Implementation**:
- [ ] SRS complete and approved by product owner
- [ ] Architecture reviewed by tech lead
- [ ] No [BLOCKER] or [CLARIFICATION NEEDED] tags in spec
- [ ] Acceptance criteria are testable

**Before Implementation → Review**:
- [ ] All tests passing (local + CI)
- [ ] Code coverage adequate (80% backend, 75% frontend)
- [ ] No merge conflicts
- [ ] All CI checks green

**Before Review → Approval**:
- [ ] Review agent approves (or changes requested)
- [ ] Code reviewers approve
- [ ] All issues resolved

**Before Approval → Deploy**:
- [ ] Human approval (tech lead/manager)
- [ ] Deployment plan ready
- [ ] Rollback plan ready

---

## Escalation & Issue Handling

###  If Planning Blocked

Mark spec with `[BLOCKER]`:
```markdown
## User Authentication

[BLOCKER] Backend needs clarification:
- Duration of JWT token? (24h? 7d?)
- Should token refresh be automatic or manual?
- Where to store token client-side?

Cannot proceed with frontend guide until answered.
Status: Awaiting response
Contact: [Backend tech lead]
```

**Action**: Planner pauses, flags for product owner/tech lead, waits for clarification, updates spec, resumes.

### If Implementation Blocked

Create GitHub issue linking to blocker:
```markdown
# Blocker: [Issue]

Reference: Feature spec [link]

Blocker: [What's preventing progress]
Blocked By: [What needs to happen first]
Blocked Duration: [How long blocked]

Timeline Impact: [Estimated days delayed]
```

**Action**: Flag to tech lead, assign unblocking work, resume when resolved.

### If Review Fails

Review agent provides specific feedback:
```markdown
# Changes Requested: [Feature]

Backend Issue 1:
- File: [src/handlers/dashboard.handler.ts:45]
- Problem: N+1 query loading users (loading each user's posts separately)
- How to fix: Use batch query to load all posts at once
- Impact: Criterion #3 "Performance targets met"

Frontend Issue 1:
- File: [src/pages/Dashboard.tsx:120]
- Problem: Table not accessible (no aria-label on rows)
- How to fix: Add aria-label describing row content
- Impact: Criterion #5 "Accessibility verified"
```

**Action**: Dev team fixes issues, resubmits PR, re-review.

---

## Communication Template

### Planning Phase Announcement

```
Subject: [Feature] Planning Phase Starting

The Project Planner Agent is analyzing the [feature] SRS.

Timeline:
- Start: [Date]
- Complete: [Date]
- Output: Feature spec + backend/frontend guides

The spec will be available at: [Link]

If you need to ask questions during planning, comment on [Issue/PR].
```

### Review Complete Announcement

```
Subject: [Feature] Ready for Merge

Review/QA Agent has completed review. Status: ✅ APPROVED

What was verified:
- ✅ Specification: 10/10 acceptance criteria met
- ✅ Backend: Tests passing (85% coverage)
- ✅ Frontend: Responsive, accessible, performant (Lighthouse 94)
- ✅ Integration: End-to-end workflows working
- ✅ Security: No vulnerabilities found
- ✅ Performance: All targets met

PRs ready to merge:
- Backend: [#123]
- Frontend: [#124]

Next: Awaiting human approval from [Tech Lead]
```

---

## Metrics & Monitoring

Track each workflow phase:

| Phase | Target | Metric | Owner |
|-------|--------|--------|-------|
| SRS | 5-10 days | Days to complete | Product Owner |
| Planning | 5-10 days | Days to complete | Tech Lead |
| Backend | 7-15 days | Days to complete | Backend Lead |
| Frontend | 7-15 days | Days to complete | Frontend Lead |
| Review | 3-5 days | Days to complete | Tech Lead |
| Deploy | 1-2 days | Days to complete | DevOps |
| **Total** | **28-56 days** | **Days from SRS to prod** | **Project Manager** |

---

## Troubleshooting

| Problem | Cause | Solution |
|---------|-------|----------|
| Planning taking too long | Ambiguous SRS | Product owner clarifies requirements |
| Backend delayed | API spec unclear | Backend agent raises questions in spec |
| Review rejecting code | Spec not implemented | Dev team reviews spec and acceptance criteria, makes changes |
| Performance failing | Unoptimized queries | Backend team optimizes queries per memory.md patterns |
| Accessibility failing | Missing ARIA labels | Frontend team adds accessibility per WCAG requirements |

---

**Workflow Version**: 1.0  
**Last Updated**: February 23, 2026  
**Questions?** Reference the agent guides or escalate to tech lead
