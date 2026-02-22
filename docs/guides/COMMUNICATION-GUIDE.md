# Communication Guide: Working with Copilot Agents

This guide shows how you and your team can communicate requirements and specifications to Copilot agents, and how to work together efficiently using our project setup.

---

## Visual Overview: Communication Workflow

```
📥 INPUT (Your Team)              🎯 ROUTING              🤖 AGENTS                    ✅ OUTPUT
─────────────────                ──────────              ───────────                  ──────────
📋 SRS                             Complete Feature       🎭 Orchestration             PR Ready
📊 ER Diagram        ──────────→    │  Spec + Diagrams    ├─ Project Planner          Code + Tests
🔄 Sequence Diagram   Decision    Architecture          ├─ Backend Implementation    Specs
📝 Spec Points        Tree         │  Quick Code          ├─ Frontend Implementation   Analysis +
                                   │  Improvement        └─ Review/QA                ADR
                                   Code Tests           🏗️ Architecture              
                                   Security Review      └─ plan.agent               
```

**Full Visual Diagram**: See [Communication Workflow Diagram](COMMUNICATION-WORKFLOW.md)

---

## Quick Start: Communication Formats

Your team can communicate with Copilot agents using these formats:

```
📋 SRS (Software Requirements Specification)
   → Best for: Complete feature development
   → Format: Structured requirements document
   → Use with: Orchestration System workflow
   → Output: Feature specs, implementation guides, diagrams

📊 ER Diagram (Entity Relationship)
   → Best for: Database/data structure discussions
   → Format: Visual diagram or description
   → Use with: Architecture discussions or implementation specs
   → Output: Refined schema, migration plans, data validations

🔄 Sequence Diagram
   → Best for: Complex workflows, integrations, API flows
   → Format: Visual diagram or step-by-step flow
   → Use with: Implementation specifications or architecture decisions
   → Output: Implementation approach, error handling strategies

📝 Simple Spec Points
   → Best for: Quick features, bug fixes, improvements
   → Format: Bullet points or short description
   → Use with: Direct implementation or architectural decisions
   → Output: Code implementation or analysis

🎯 Hybrid
   → Best for: Comprehensive specifications
   → Format: SRS + Diagrams + Spec points together
   → Use with: Any agent (they'll reference all inputs)
   → Output: Complete feature with all perspectives
```

---

## Workflow Decision Tree: How to Communicate

```
START: What do you want to do?

├─ "I have complete requirements and need a feature built"
│  ├─ Format: SRS + ER Diagram + Sequence Diagram
│  ├─ Interaction: "I'll start the orchestration workflow"
│  └─ Command: Follow [Complete Feature Development](#complete-feature-development) section
│
├─ "I want to discuss architectural approach first"
│  ├─ Format: Simple Spec Points + Diagrams
│  ├─ Interaction: "Let's validate architecture before building"
│  └─ Command: Follow [Architecture Discussion](#architecture-discussion) section
│
├─ "I have a quick feature or bug fix"
│  ├─ Format: Simple Spec Points
│  ├─ Interaction: "Implement this with context from our setup"
│  └─ Command: Follow [Quick Implementation](#quick-implementation) section
│
├─ "I want to improve/refactor existing code"
│  ├─ Format: Code snippet + Spec points
│  ├─ Interaction: "Here's what I want to improve, follow our patterns"
│  └─ Command: Follow [Code Improvement](#code-improvement) section
│
└─ "I need to understand a technical decision"
   ├─ Format: Question + Context
   ├─ Interaction: "Why did we choose X over Y?"
   └─ Command: Follow [Decision Discussion](#decision-discussion) section
```

---

## Communication Scenarios

### Scenario 1: Complete Feature Development

**Use This When**: You have comprehensive requirements (SRS) and want a feature built end-to-end.

#### Step 1: Prepare Your Requirements

Create an **SRS document** using the template:

```markdown
[Reference: .github/templates/handoffs/srs-input-template.md]

# Feature: [Feature Name]

## Overview
[What problem does this solve?]

## User Stories
- As a [user type], I want to [action], so that [benefit]
- ...

## Functional Requirements
- [Requirement 1]
- [Requirement 2]

## Data Entities
[List key entities with attributes]

## API Endpoints
- GET /api/...
- POST /api/...

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2
```

#### Step 2: Optionally Add Diagrams

```markdown
## Entity Relationship Diagram

[Describe or attach ER diagram showing data relationships]

Example:
- User (id, name, email)
- Posts (id, userId, title, content)
- Comments (id, postId, userId, text)

Relationships:
- User → Posts (1 to many)
- Posts → Comments (1 to many)
```

```markdown
## Sequence Diagram / Workflow

[Describe or attach sequence showing feature flow]

Example user journey:
1. User visits dashboard
2. System fetches user data from DB
3. System displays profile + recent posts
4. User clicks "New Post"
5. System opens form modal
6. User submits, system validates & saves
7. System redirects to post view
```

#### Step 3: Communicate with Project Planner Agent

**Command to run in Copilot chat:**

```markdown
I'm starting the orchestration workflow for a new feature.

**Feature**: [Feature Name]
**Project**: backend-api [or frontend-web]

I have prepared:
- [x] SRS with requirements
- [x] ER Diagram
- [x] Sequence Diagram

**Reference Files**:
- SRS: [paste or link to your SRS]
- Project Context: [Link to docs/projects/backend-api/context.md]
- Global Context: [Link to docs/_global/context.md]

**Request**: Use project-planner.agent.md to transform this SRS into:
1. Feature specification
2. Backend implementation guide
3. Frontend implementation guide
4. Architecture diagrams
5. Acceptance criteria checklist

SRS Content:
[Paste your full SRS here]
```

#### Step 4: Receive Specification Output

**Project Planner Agent returns:**
- ✅ Detailed feature specification
- ✅ Backend implementation guide (routes, services, database changes)
- ✅ Frontend implementation guide (components, pages, state management)
- ✅ Updated ER diagram (if needed)
- ✅ Updated sequence diagrams
- ✅ Acceptance criteria checklist

#### Step 5: Communicate with Implementation Agents

**For Backend** — Command to run:

```markdown
I have a feature specification from the project planner.

**Feature**: [Feature Name]
**Project**: backend-api

Reference:
- Backend Implementation Guide: [link]
- Backend Context: [docs/projects/backend-api/context.md]
- Backend Instructions: [.github/instructions/backend.instructions.md]
- Global Decisions: [docs/_global/memory.md]

**Request**: Use backend-implementation.agent.md to implement:
- All database migrations
- API routes and handlers
- Business logic services
- Input validation & error handling
- Comprehensive unit/integration tests (>80% coverage)

Implementation Guide:
[Paste guide from planner here]
```

**For Frontend** — Command to run:

```markdown
I have a feature specification from the project planner.

**Feature**: [Feature Name]
**Project**: frontend-web

Reference:
- Frontend Implementation Guide: [link]
- Frontend Context: [docs/projects/frontend-web/context.md]
- Frontend Instructions: [.github/instructions/frontend.instructions.md]
- Global Decisions: [docs/_global/memory.md]

**Request**: Use frontend-implementation.agent.md to implement:
- All React components following patterns
- Page layouts and routing
- State management with Context API
- API integration
- Accessibility (WCAG 2.1 AA)
- Comprehensive tests (>75% coverage)

Implementation Guide:
[Paste guide from planner here]
```

#### Step 6: Quality Review

**Command to run:**

```markdown
I have two implementation PRs ready for review.

**Feature**: [Feature Name]
**Feature Specification**: [link]
**Backend PR**: [link]
**Frontend PR**: [link]

**Request**: Use review-qa.agent.md to verify:
1. Both implementations match the feature specification
2. All acceptance criteria are satisfied
3. Code quality meets standards
4. Test coverage meets requirements (backend >80%, frontend >75%)
5. Security review passes
6. Accessibility is verified (frontend)

Return: APPROVED or CHANGES REQUESTED with specific issues
```

---

### Scenario 2: Architecture Discussion

**Use This When**: You want to validate technical approach before building.

#### Example: Deciding on Caching Strategy

**Prepare Your Discussion:**

```markdown
Architecture Decision: Caching Strategy for API

Current State:
- Express API with PostgreSQL
- Average response time: 200ms
- Peak load: 1000 req/sec

Problem:
- Some endpoints returning the same data are slow
- User profile requests hitting DB repeatedly

Options to Evaluate:
1. Redis in-memory cache
2. Database query optimization
3. MySQL query cache
4. HTTP caching headers

Constraints:
- Budget: Low
- Team familiarity: Moderate with Redis
- Real-time requirement: <1 minute staleness acceptable
```

**Command to run:**

```markdown
I need to make an architectural decision.

**Decision**: Choose caching strategy for our API

Reference:
- Global Context: [docs/_global/context.md]
- Global Decisions: [docs/_global/memory.md]
- Backend Context: [docs/projects/backend-api/context.md]

Request: Use plan.agent.md to:
1. Analyze each option against our constraints
2. Provide recommendation with justification
3. Include implementation roadmap
4. Create ADR (Architecture Decision Record) template

Current State & Options:
[Paste your decision discussion here]
```

**Agent Returns:**
- ✅ Analysis of each option
- ✅ Recommendation with pros/cons
- ✅ Implementation roadmap
- ✅ ADR template to document decision
- ✅ Updated `docs/_global/memory.md` entry

**Then Execute:**

Once approved, communicate implementation:

```markdown
We've decided to implement Redis caching.

**Decision**: [link to approved ADR]

Request: Implement the following using domain instructions:
- Redis client setup in Express
- Cache layer service
- Cache invalidation strategy
- Tests for cache behavior

Reference: [ADR implementation guide from plan.agent]
```

---

### Scenario 3: Quick Implementation

**Use This When**: You have a clear feature or bug fix that doesn't need orchestration.

#### Example: Add New API Endpoint

**Prepare Spec Points:**

```markdown
Feature: Add team member endpoint

Spec Points:
- POST /api/teams/{teamId}/members
- Request body: { email, role: "admin|member" }
- Response: Member object with id, email, role, createdAt
- Validation: Email must be valid, role must be in [admin, member]
- Authorization: Only team admins can add members
- Error: Return 400 if email already in team, 403 if not admin
- Tests: Unit tests for validation, integration test for happy path

Related:
- ER: Team (1) → (many) Members
- Existing endpoint: GET /api/teams/{teamId}/members (use as reference)
```

**Command to run:**

```markdown
Quick feature implementation needed.

**Feature**: Add team member endpoint
**Project**: backend-api

Request: Implement following domain instructions and project patterns:
- implement-from-spec prompt
- Follow [Backend Instructions](.github/instructions/backend.instructions.md)
- Follow [API Path Instructions](.github/instructions/paths/api.instructions.md)
- Reference existing similar endpoints for patterns

Spec:
[Paste your spec points here]
```

**Alternative: For Frontend**

```markdown
Quick feature implementation needed.

**Feature**: Add team member form modal
**Project**: frontend-web

Request: Implement following domain instructions and project patterns:
- implement-from-spec prompt
- Follow [Frontend Instructions](.github/instructions/frontend.instructions.md)
- Follow [Component Path Instructions](.github/instructions/paths/components.instructions.md)
- Accessibility: WCAG 2.1 AA

Spec:
- Modal form with email input + role dropdown
- Submit button with loading state
- Error display below form
- Success toast notification
- Close button and Escape key handler
- Tests: Form validation, submit handling

Reference existing: [similar component if any]
```

---

### Scenario 4: Code Improvement / Refactoring

**Use This When**: You want to improve existing code following project patterns.

#### Example: Refactor Service for Better Testing

**Prepare Your Request:**

```markdown
Code to improve:
[Copy existing service code here - 20-50 lines]

Current issues:
- Tight coupling to database layer
- Difficult to mock in tests
- No error handling separation

Goal:
- Dependency injection pattern
- Better testability
- Follows project service patterns

Context:
- File: src/services/userService.ts
- project: backend-api
```

**Command to run:**

```markdown
Code refactoring request.

**File**: src/services/userService.ts
**Project**: backend-api

Request: Use refactor-slice prompt to:
- Improve testability (dependency injection)
- Follow [Service Instructions](.github/instructions/paths/services.instructions.md)
- Match patterns in [Backend Context](.github/projects/backend-api/context.md)
- Zero functionality change (only structure)
- Include tests for refactored code

Current Code:
[Paste code here]

Files to Reference for Patterns:
- [Similar service example]
- [Backend testing guide]
```

**After Refactoring:**

```markdown
Code refactoring complete. 

Request: Update memory with lessons learned.

What We Learned:
- Dependency injection improves testability
- New pattern can be reused in [other services]
- Reduces mock complexity in tests

Please update: docs/projects/backend-api/memory.md
```

---

### Scenario 5: Test Generation

**Use This When**: You need comprehensive test coverage for code.

#### Example: Generate Tests for Complex Function

**Prepare Your Code:**

```markdown
Function to test:

export const calculateUserScore = (user: User): number => {
  let score = user.reputation || 0;
  
  if (user.isVerified) score += 50;
  if (user.isPremium) score += 100;
  
  const postCount = user.posts?.length || 0;
  score += postCount * 5;
  
  if (user.lastActive) {
    const daysSinceActive = Math.floor(
      (Date.now() - user.lastActive.getTime()) / (1000 * 60 * 60 * 24)
    );
    if (daysSinceActive < 7) score *= 1.5;
  }
  
  return Math.floor(score);
};
```

**Command to run:**

```markdown
Test generation request.

**File**: src/services/scoringService.ts
**Project**: backend-api

Request: Use test-gen prompt to generate:
- Unit tests covering all branches
- Edge cases (null values, missing fields)
- Boundary conditions
- Performance considerations
- Follow testing patterns from [path instructions]

Target coverage: >95%

Function to test:
[Paste function here]

Type definitions:
[Paste User type here]
```

---

### Scenario 6: Security Review

**Use This When**: You want vulnerability and security best practice checks.

#### Example: Review API Endpoint

**Prepare Your Code:**

```markdown
Endpoint to review:

POST /api/users/{userId}/update
- Updates user profile
- Takes: name, email, bio, avatar
- No explicit authorization check
- Direct DB update without validation
```

**Command to run:**

```markdown
Security review request.

**File**: src/handlers/userUpdateHandler.ts
**Project**: backend-api

Request: Use security-review prompt to:
- Identify vulnerabilities
- Check authorization patterns
- Validate input sanitization
- Review SQL injection risks
- Check response data (no secrets leaked)
- Recommend fixes following [Backend Instructions]

Code to review:
[Paste handler code here]

Related code:
[Link to authentication/authorization middleware]
```

---

## Template: Communication Protocol

Use this template when communicating with any agent:

```markdown
# [Agent Name] Request

## Context
- **Project**: [backend-api | frontend-web | devops]
- **Feature/Task**: [Name]
- **Priority**: [High/Medium/Low]
- **Timeline**: [Needed by: date]

## What I'm Asking For
[Clear, specific request - 2-3 sentences]

## Input References
- [Link to specification / context / existing code]
- [Link to project context]
- [Link to domain instructions]
- [Link to path instructions if applicable]

## Input Content
[Paste or link to SRS, code, diagrams, etc.]

## Success Criteria
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Test coverage: [%]
- [ ] Meets project patterns? ✅

## Additional Context
[Any special constraints, gotchas, or dependencies]
```

---

## Input Formats & Examples

### Format 1: SRS (Full Feature)

**Best for**: Complete features via orchestration

```markdown
# SRS: Feature Name

## 1. Overview
Brief description of what this feature does.

## 2. User Stories
- As a [user type], I want [action], so that [benefit]

## 3. Functional Requirements
- REQ-1: [Requirement]
- REQ-2: [Requirement]

## 4. Non-Functional Requirements
- Performance: [target]
- Security: [considerations]
- Accessibility: [standards]

## 5. Data Entities
| Entity | Attributes |
|---|---|
| User | id, name, email |
| Post | id, userId, title |

## 6. API Contracts
```json
{
  "POST /api/endpoint": {
    "request": {...},
    "response": {...},
    "errors": [...]
  }
}
```

## 7. Acceptance Criteria
- [ ] Users can [action]
- [ ] Data validates [validation]
- [ ] Tests pass and coverage > 80%
```

### Format 2: ER Diagram

**Best for**: Database discussions, architecture reviews

```markdown
## ER Diagram

Entity: User
- id: UUID (primary key)
- email: String (unique)
- name: String
- createdAt: DateTime

Entity: Post
- id: UUID (primary key)
- userId: UUID (foreign key)
- title: String
- content: Text
- createdAt: DateTime

Relationships:
- User (1) ← → (many) Post [userId → id]
```

Or attach visual diagram.

### Format 3: Sequence Diagram

**Best for**: Complex workflows, integrations

```markdown
## Sequence Diagram: User Login Flow

1. User enters credentials on login form
2. Frontend validation checks format
3. Frontend POST /api/auth/login with credentials
4. Backend validates credentials against DB
5. Backend generates JWT token
6. Backend returns token + user profile
7. Frontend stores token in localStorage
8. Frontend redirects to dashboard
```

Or attach visual diagram.

### Format 4: Spec Points (Quick)

**Best for**: Bug fixes, quick features, improvements

```markdown
## Quick Spec: Add Password Reset Email

Points:
- POST /api/auth/forgot-password
- Input: email (required, valid email)
- Output: { message: "Email sent" }
- Validation: Email exists in system
- Email: Contains reset link + expiry (24h)
- Security: Rate limit (max 5 per hour per email)
- Error: Return 400 if invalid, 404 if not found
- Tests: Happy path, rate limit, invalid email
```

---

## What to Expect from Each Agent

### Project Planner Agent
**Input**: SRS + Diagrams  
**Output**:
- ✅ Feature specification (complete)
- ✅ Backend implementation guide (routes, services, DB changes)
- ✅ Frontend implementation guide (components, pages, state)
- ✅ Architecture diagrams (updated if needed)
- ✅ Acceptance criteria checklist
- ✅ Implementation roadmap

**Timeline**: 2-5 days

### Backend Implementation Agent
**Input**: Implementation guide + Context  
**Output**:
- ✅ Database migrations
- ✅ API routes and handlers
- ✅ Business logic services
- ✅ Input validation and error handling
- ✅ Comprehensive tests (>80% coverage)
- ✅ PR ready for review
- ✅ Documentation of changes

**Timeline**: 3-7 days

### Frontend Implementation Agent
**Input**: Implementation guide + Context  
**Output**:
- ✅ React components (following patterns)
- ✅ Pages and routing
- ✅ State management setup
- ✅ API integration
- ✅ Accessibility verification (WCAG 2.1 AA)
- ✅ Comprehensive tests (>75% coverage)
- ✅ PR ready for review
- ✅ Documentation of changes

**Timeline**: 3-7 days

### Review/QA Agent
**Input**: Both PRs + Feature spec  
**Output**:
- ✅ Spec compliance verification
- ✅ Test coverage analysis
- ✅ Security findings
- ✅ Code quality assessment
- ✅ Accessibility audit (frontend)
- ✅ APPROVED / CHANGES REQUESTED decision

**Timeline**: 1-2 days

### plan.agent (Architectural Decisions)
**Input**: Decision context + Options to evaluate  
**Output**:
- ✅ Option analysis
- ✅ Recommendation with justification
- ✅ Implementation roadmap
- ✅ ADR (Architecture Decision Record)
- ✅ Updated docs/_global/memory.md with decision

**Timeline**: 1-3 days

---

## Examples: Real Communication

### Example 1: Starting Orchestration Workflow

**Human says:**
```
I'm starting orchestration for a new feature.

Feature: User Dashboard with Analytics

SRS:
[full SRS with requirements, user stories, diagrams]

Project: frontend-web

Let's start with project planner.
```

**Copilot says:**
```
✅ I'll use project-planner.agent.md to build specifications.

Expected output:
- Feature specification (pages, components, endpoints needed)
- Frontend implementation guide (React structure, state, routing)
- Backend integration points
- Mockup sequence diagrams
- Acceptance criteria

Timeline: 2 days

Should I proceed?
```

**Human says:**
```
Yes, proceed.
```

[Agent works...]

**Agent says:**
```
✅ Specifications complete!

Frontend Implementation Guide:
- 5 components (ChartCard, DateRange, FilterPanel, DashboardLayout, AnalyticsPage)
- 2 pages (Dashboard, Detailed Analytics)
- API calls: GET /api/analytics/summary, GET /api/analytics/data
- State: Use Context for filters and date range
- Accessibility: Focus management, ARIA labels

[Full guide provided]

Next: Use frontend-implementation.agent.md with this guide.
```

**Human says:**
```
Great! Now implement the frontend.

I have the implementation guide. Let's build the frontend following our patterns.

Frontend Implementation Guide: [from above]

Project context: docs/projects/frontend-web/context.md
Frontend instructions: .github/instructions/frontend.instructions.md
```

**Copilot says:**
```
✅ Using frontend-implementation.agent.md

I'll build:
- All React components following your patterns
- State management with Context API
- API integration layer
- Tests with >75% coverage
- Accessibility verification

Timeline: 5 days

PR will include:
- /src/components/analytics/*
- /src/pages/Dashboard.tsx
- /src/services/analyticsService.ts
- /src/__tests__/* tests

Should I proceed?
```

---

### Example 2: Quick Architecture Decision

**Human says:**
```
Quick decision needed: Database for real-time notifications?

Options:
1. Push with Redis pub/sub
2. WebSocket with polling
3. Firebase Realtime DB

Team: 2 backend devs
Timeline: Need decision in 2 days
Constraints: Want to keep stack simple
```

**Copilot says:**
```
✅ Using plan.agent.md for architectural decision

I'll analyze:
- Technical fit with our Express stack
- Team skill requirements
- Real-time performance
- Cost implications
- Implementation complexity

Timeline: 1 day

I'll provide:
- Analysis of each option
- Recommendation
- ADR template
- Implementation roadmap
```

[Agent analyzes...]

**Agent says:**
```
✅ Decision Analysis Complete

RECOMMENDATION: Redis pub/sub

Reasoning:
- Fits existing tech stack (Express + Redis already available)
- Simplest to implement for team skill level
- Proven for real-time notifications
- Cost effective
- Better performance than polling

ADR: docs/_global/adr/002-real-time-notifications.md

[Full analysis + ADR template provided]

Next: Implement following backend-implementation via implement-from-spec.
```

---

### Example 3: Quick Implementation Request

**Human says:**
```
Quick feature to implement.

Feature: Add email notification preferences endpoint

Spec:
- PUT /api/users/{userId}/notification-settings
- Body: { emailNotifications: bool, frequency: "daily|weekly|off" }
- Response: Updated settings
- Validation: frequency must be valid value
- Auth: Only own user can update
- Error: 400 if invalid, 403 if not authorized

Reference existing user settings endpoint as pattern.
```

**Copilot says:**
```
✅ Quick implementation

Using implement-from-spec with:
- Backend instructions: API patterns
- Path instructions: API endpoints
- Project patterns from context
- Security from existing auth middleware

I'll build:
- Route handler
- Validation logic
- Update logic with error handling
- Unit + integration tests

Timeline: 1 day

PR ready for merge after QA.
```

---

## Troubleshooting: When Communication Goes Wrong

| Issue | Solution |
|-------|----------|
| "Agent didn't understand my spec" | Provide more context. Use template format. Add example of what you want. |
| "Output doesn't match our patterns" | Reference specific instructions file in request. Example: "Follow .github/instructions/paths/api.instructions.md" |
| "Wrong project context used" | Explicitly state project. Example: "Project: backend-api" at the top. |
| "Missing test coverage" | Specify coverage target. Example: "Tests required: >85% coverage" |
| "Output needs changes" | Provide specific feedback. Example: "Component needs error boundary, add try/catch around API call" |
| "Timeline not met" | Break feature into smaller specs. Use scenario-based approach. |
| "Unclear if work is done" | Ask for acceptance criteria check. "Does this meet all criteria from spec?" |

---

## Best Practices

✅ **DO**:
- Provide complete context (link to docs/projects/*/context.md)
- Reference specific instructions you want followed
- Include acceptance criteria
- Specify test coverage requirements
- Say what project (backend-api | frontend-web)
- Use templates provided
- Provide examples of styling/patterns if custom

❌ **DON'T**:
- Give vague requirements like "build a dashboard"
- Forget to mention project
- Skip security/accessibility requirements
- Assume agent knows your patterns without linking docs
- Request multiple conflicting things in one ask
- Skip the SRS for complex features

---

## Quick Reference: Which Format to Use

| Task | Format | Use Agent |
|------|--------|-----------|
| Build complete feature | SRS + Diagrams | Orchestration (4-8 weeks) |
| Validate approach first | Spec points + diagrams | plan.agent (1-3 days) |
| Implement from spec | Spec guide | Implementation agent (3-7 days) |
| Quick feature | Spec points | impl-from-spec prompt (1-2 days) |
| Bug fix | Code + spec points | impl-from-spec or refactor-slice (hours) |
| Improve code | Code + goal | refactor-slice prompt (hours) |
| Generate tests | Code + coverage target | test-gen prompt (hours) |
| Security check | Code or endpoint | security-review prompt (1 day) |
| Architecture choice | Decision + options | plan.agent (1-3 days) |

---

**Last Updated**: February 23, 2026  
**Next**: Read [.github/agents/AGENT-REFERENCE.md](.github/agents/AGENT-REFERENCE.md) for agent details
