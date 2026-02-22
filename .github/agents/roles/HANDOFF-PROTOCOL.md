# Role-Based Agent Handoff Protocol

Defines how role-based agents work together sequentially and how work is handed off between them.

---

## Handoff Protocol Overview

When multiple specialized agents work on the same feature, they follow a strict handoff protocol to ensure quality and continuity.

```
Agent 1 Output
    ↓
Handoff Point 1: Validate output quality
    ↓
Pass to Agent 2 ✅
    ↓
Agent 2 Input (uses Agent 1's output)
Agent 2 Work (builds upon Agent 1's foundation)
Agent 2 Output
    ↓
Handoff Point 2: Validate quality, dependencies met
    ↓
Pass to Agent 3 ✅
    ↓
[Continue until final agent]
```

---

## Handoff Points

### Handoff Point 1: Project Manager → Architect

**Trigger**: Project Manager completes feature breakdown

**What Gets Passed**:
```markdown
1. Feature breakdown document
   - List of features with dependencies
   - Estimated timeline per feature
   
2. Scope boundaries document
   - What's IN scope
   - What's OUT of scope
   - Assumptions made
   
3. Acceptance criteria clarity document
   - Clarified user stories
   - Measurable acceptance criteria
   - Edge cases identified
   
4. Constraints & dependencies
   - Technical constraints
   - Third-party dependencies
   - Timeline constraints
```

**Validation Before Handoff**:
- ❌ Are all requirements from SRS covered?
- ❌ Are dependencies clearly mapped?
- ❌ Is timeline realistic?
- ❌ Are scope boundaries clear?

**If Validation Fails**:
→ Project Manager revises and re-submits

**Architect Response**:
```
Architect acknowledges receipt:
"✅ Received feature breakdown. Starting technical architecture design."
```

---

### Handoff Point 2: Architect → API Designer & Backend API Specialist

**Trigger**: Architect completes technical architecture

**What Gets Passed to API Designer**:
```markdown
1. API Contracts document
   - Endpoint specifications
   - Request/response schemas
   - Error responses
   
2. Data Model
   - Entity relationships
   - Field definitions
   - Constraints
   
3. Integration points
   - External services to integrate
   - Webhook requirements if any
   - Authentication approach
```

**What Gets Passed to Backend API Specialist**:
```markdown
1. API Implementation Guide
   - Tech stack decisions
   - Express patterns to follow
   - Service layer structure
   
2. Data Model & Migrations
   - Table schemas
   - Indexes needed
   - Initial data
   
3. Business Logic Outline
   - Core algorithms
   - Validation rules
   - Error handling approach
```

**Validation Before Handoff**:
- ❌ Are API contracts complete?
- ❌ Is data model normalized?
- ❌ Are tech stack decisions justified?
- ❌ Are implementation guides clear?

**If Validation Fails**:
→ Architect revises and re-submits

---

### Handoff Point 3: Architect → Frontend Specialist

**Trigger**: Architect completes frontend architecture

**What Gets Passed**:
```markdown
1. Frontend Implementation Guide
   - Page structure & routing
   - Component breakdown
   - State management approach
   - API endpoints to integrate
   
2. Design Specifications
   - Color scheme
   - Typography
   - Component patterns
   - Responsive breakpoints
   
3. Accessibility Requirements
   - WCAG 2.1 AA compliance
   - Keyboard navigation
   - Screen reader requirements
   
4. Performance Targets
   - Page load targets
   - LCP (Largest Contentful Paint)
   - Interaction time targets
```

**Validation Before Handoff**:
- ❌ Are component requirements clear?
- ❌ Are API contracts specified?
- ❌ Are accessibility needs documented?
- ❌ Are performance metrics defined?

---

### Handoff Point 4: Architect → Data Analyst

**Trigger**: Architect completes data model

**What Gets Passed**:
```markdown
1. Data Model Specification
   - Entity-relationship diagram
   - Table schemas with constraints
   - Field data types and sizes
   
2. Performance Requirements
   - Query patterns expected
   - Expected data volume
   - Growth projections
   
3. Business Rules
   - Data validation rules
   - Uniqueness constraints
   - Referential integrity
   
4. Maintenance Requirements
   - Backup/restore strategy
   - Data retention policies
   - Archival strategies
```

**Validation Before Handoff**:
- ❌ Is data model complete?
- ❌ Are constraints clear?
- ❌ Are performance needs documented?
- ❌ Are migration paths clear?

---

### Handoff Point 5: API Designer → Backend API Specialist

**Trigger**: API Designer completes OpenAPI specs

**What Gets Passed**:
```markdown
1. OpenAPI/Swagger Specification
   - All endpoints defined
   - Request schemas (request body + query params)
   - Response schemas for all status codes
   - Error responses documented
   
2. Examples & Use Cases
   - Real-world examples
   - Common integration flows
   - Error scenarios
   
3. Non-Functional Requirements
   - Rate limiting
   - Pagination strategy
   - Caching strategy
   - Versioning approach
   
4. Validation Rules
   - Input validation rules
   - Business rule validations
   - Error messages
```

**Validation Before Handoff**:
- ❌ Are all endpoints defined?
- ❌ Are schemas complete?
- ❌ Are examples clear?

**Backend Response**:
```
Backend Implementation Agent:
"✅ Received API spec. Implementing Express routes per contract."
```

---

### Handoff Point 6: Data Analyst → Backend API Specialist

**Trigger**: Data Analyst completes migrations

**What Gets Passed**:
```markdown
1. Database Migrations
   - Knex/Sequelize migration files
   - Indexes defined
   - Constraints applied
   
2. Seed Scripts
   - Test data generation
   - Reference data
   - Initial values
   
3. Query Optimization Notes
   - Recommended indexes
   - Join optimization tips
   - N+1 query patterns to avoid
   
4. Backup/Restore Scripts
   - How to backup production
   - How to restore from backup
```

**Validation Before Handoff**:
- ❌ Are all migrations valid?
- ❌ Do indexes align with query patterns?
- ❌ Are seed scripts reproducible?

---

### Handoff Point 7: Backend API Specialist & Frontend Specialist → Security Expert

**Trigger**: Both implementation agents complete their work

**What Gets Passed**:
```markdown
1. Backend Code PR
   - All routes, handlers, services
   - Tests with >80% coverage
   - Documentation comments
   
2. Frontend Code PR
   - All components, pages, styling
   - Tests with >75% coverage
   - Accessibility verified
   
3. Deployment Configuration
   - Environment variables
   - Secrets management
   - Configuration approach
   
4. Security Checklist for Audit
   - Known vulnerabilities (if any)
   - Security assumptions made
   - Areas needing review
```

**Validation Before Handoff**:
- ❌ Is test coverage adequate?
- ❌ Are all endpoints implemented?
- ❌ Is code following patterns?

**Security Expert Response**:
```
Security Expert:
"✅ Received code. Starting security audit & vulnerability scan."
```

---

### Handoff Point 8: Implementation Agents & Security Expert → Documentation Specialist

**Trigger**: All implementation and security review complete

**What Gets Passed**:
```markdown
1. Implementation Code
   - Final PR with all code
   - Implemented features
   - Tests passing
   
2. Architecture Decisions Document
   - Why certain choices were made  
   - Trade-offs considered
   
3. Setup & Deployment Guide
   - How to run locally
   - How to deploy
   - Configuration needed
   
4. API Specification (if applicable)
   - OpenAPI spec
   - Endpoint examples
   - Integration examples
```

**Validation Before Handoff**:
- ❌ Is code production-ready?
- ❌ Are all features working?

---

### Handoff Point 9: All Agents → Review/QA Agent

**Trigger**: All implementation, security, and documentation complete

**What Gets Passed**:
```markdown
1. Backend PR
   - Code, tests, migrations
   - Ready for merge
   
2. Frontend PR
   - Code, tests, accessibility
   - Ready for merge
   
3. Feature Specification
   - Original SRS
   - All acceptance criteria
   - Architecture documents
   
4. Test Coverage Report
   - Backend coverage >80%
   - Frontend coverage >75%
   - Security audit results
   
5. Documentation
   - API docs
   - User guides
   - Setup instructions
   
6. Issue Summary
   - Any outstanding issues
   - Known limitations
   - Future improvements
```

**Validation Before Handoff**: Already done by previous agents

**QA Response**:
```
Review/QA Agent:
"✅ Received all materials. Starting final verification & acceptance testing."
```

---

## Quality Gate Validations

At each handoff, the receiving agent validates:

### Agent Responsibility Check
- Does the output match the expected scope for this agent?
- Are required fields/sections present?
- Is output format correct?

### Quality Check
- Is the output production-ready?
- Are there obvious errors or gaps?
- Does output follow project patterns?

### Completeness Check
- Are all requirements addressed?
- Are dependencies resolved?
- Is documentation adequate?

### Clarity Check
- Is the output clear enough for next agent?
- Are assumptions documented?
- Are decision reasons explained?

### If Validation FAILS

```
Receiving Agent:
"❌ Validation failed. Issues:
- Missing: [specific gaps]
- Unclear: [what needs clarification]
- Non-compliant: [what violates patterns]

Sending Agent: [receives feedback]
Fix issues
Re-submit
Receiving Agent: [validates again]
If passes → Continue
If fails → Escalate to leadership"
```

---

## Handoff Communication Template

**Sending Agent Uses**:

```markdown
# Handoff: [Agent Role] → [Next Agent Role]

**Feature**: [Feature Name]
**Sending Agent**: [Your Role]
**Receiving Agent**: [Next Agent]
**Date**: [Date]

## Work Completed
- ☑️ [Deliverable 1]
- ☑️ [Deliverable 2]
- ☑️ [Deliverable 3]

## Quality Validation
- ✅ All requirements addressed
- ✅ Follows project patterns  
- ✅ Documentation complete
- ✅ No blocking issues

## Special Notes
- [Any special considerations]
- [Known limitations]
- [Assumptions made]

## Files Passed
- [File/Link 1]
- [File/Link 2]

**Ready for next agent**: YES ✅

---
Handoff by: [Agent Name]
Date: [Date]
```

**Receiving Agent Uses**:

```markdown
# Handoff Acknowledgment: [Agent Role] → [Previous Agent Role]

**Feature**: [Feature Name]
**Received From**: [Previous Agent]

## Validation Results
- ✅ All deliverables received
- ✅ Quality gates passed
- ✅ Ready to begin work

## Work Beginning
- Starting: [Date]
- Timeline: [X days]
- Deliverables: [List]

**Acknowledged by**: [Agent Name]
**Date**: [Date]
```

---

## Multi-Agent Same-Feature Example

### Feature: Payment Processing Implementation

```
DAY 1-2: Project Manager Agent (SRS breakdown)
          Output: Feature breakdown
              ↓
DAY 3-5: Architect Agent (technical design)
          Output: API contracts, data model, implementation guides
              ↓
         ├─ API Designer (prep contracts)
         │     ↓
         ├─ Data Analyst (create migrations)
         │     ↓
         ├─ Backend API Specialist (implement API)
         │     Parallel: Frontend Specialist (payment form)
         │     ↓
         └─ Both complete
              ↓
DAY 10-12: Security Expert (audit payment code, PCI compliance)
           Output: Security findings, remediation
              ↓
DAY 13-14: Documentation Specialist (API docs, integration guide)
           Output: Complete documentation
              ↓
DAY 15: Review/QA Agent (final verification)
        Output: APPROVED or CHANGES REQUESTED
              ↓
DAY 16: [Merge & Deploy]
```

**Timeline**: 16 days total for complex payment feature

---

## Rules for Effective Handoffs

✅ **DO**:
- Always validate before handing off
- Document assumptions and decisions
- Provide clear examples
- Include links to reusable components
- Explain why decisions were made
- Flag potential issues upfront

❌ **DON'T**:
- Hand off incomplete work
- Skip documentation
- Make assumptions without documenting
- Pass along known issues silently
- Change decisions without explaining  
- Hand off without validation
- Create ambiguity about next steps

---

## Escalation Path

**If validation fails repeatedly**:
1. Receiving agent flags issue
2. Sending agent revises (2nd attempt)
3. If still failing → Escalate to:
   - Project leadership
   - Have discussion about requirements
   - Clarify scope
   - Either: Revise requirements OR Accept limitations

**Typical Escalation Triggers**:
- Conflicting requirements
- Scope creep discovered
-Technical infeasibility
- Timeline mismatch
- Security/compliance issues

---

**Last Updated**: February 23, 2026

**Related Documents**:
- [AGENT-REGISTRY.md](AGENT-REGISTRY.md) — All available agents
- [AGENT-SELECTION-GUIDE.md](AGENT-SELECTION-GUIDE.md) — How to pick agents
