# Handoff Protocol

This document defines the explicit protocol for handing off work between agents. Each handoff must follow this structure to ensure clarity and prevent lost context.

---

## Handoff Structure

Every handoff consists of:

```
┌──────────────────────┐
│  1. Input Template   │  What the receiving agent gets
├──────────────────────┤
│  2. Requirements     │  Must-haves before handoff
├──────────────────────┤
│  3. Success Criteria │  When agent is done
├──────────────────────┤
│  4. Quality Gates    │  Blocking issues
├──────────────────────┤
│  5. Output Template  │  What agent must deliver
├──────────────────────┤
│  6. Next Handoff     │  Where work goes next
└──────────────────────┘
```

---

## Handoff 1: SRS → Project Planner

**When**: New feature request received

### Input Template

```
Feature: [Feature Name]
Project: [backend-api | frontend-web | ...]
Priority: [Critical | High | Medium | Low]

SRS Location: [docs/projects/{project}/srs/{feature}/SRS.md]

Context:
- Project: [docs/projects/{project}/context.md]
- Memory: [docs/projects/{project}/memory.md]
- Domain: [docs/domains/{backend|frontend}/context.md]
```

### Requirements (Before Planner Starts)
- [ ] SRS document complete and clear
- [ ] ER diagram(s) attached (if applicable)
- [ ] Current system architecture understood
- [ ] User roles and permissions defined
- [ ] Performance requirements specified
- [ ] Security requirements specified

### Success Criteria (When Planner is Done)
- [ ] Feature specification complete (feature spec template)
- [ ] ER diagrams updated/created
- [ ] Sequence diagrams created (critical flows)
- [ ] Backend implementation guide written
- [ ] Frontend implementation guide written
- [ ] Acceptance criteria defined (5-10 testable criteria)
- [ ] Assumptions documented
- [ ] Risks flagged
- [ ] Dependencies identified
- [ ] No [CLARIFICATION NEEDED] tags remain

### Quality Gates (Blocking Issues)
- ❌ Ambiguous requirements → Raise clarification
- ❌ Conflicting requirements → Flag for human review
- ❌ Missing security spec → Add default security approach
- ❌ Performance unbounded → Ask for targets
- ❌ Diagram unclear → RequestDescription refinement

### Output Template

```markdown
# Feature Spec: [Feature Name]

Status: Ready for Implementation

## Handoff Checklist
- [x] Spec complete
- [x] Diagrams clear
- [x] Backend guide ready
- [x] Frontend guide ready
- [x] Acceptance criteria defined
- [x] No ambiguities remain

## Files Created
- [Feature Spec](docs/projects/{project}/features/{feature}/spec.md)
- [Backend Guide](docs/projects/{project}/features/{feature}/backend-guide.md)
- [Frontend Guide](docs/projects/{project}/features/{feature}/frontend-guide.md)
- [ER Diagrams](docs/projects/{project}/features/{feature}/diagrams/er.md)
- [Sequence Diagrams](docs/projects/{project}/features/{feature}/diagrams/sequences.md)

## Acceptance Criteria
1. [Criterion 1] — How to verify
2. [Criterion 2] — How to verify
... (5-10 total)

## Known Issues / Flags
- [Any potential problems flagged]

## Next Steps
Hand off to Backend Implementation + Frontend Implementation (parallel).
```

### Next Handoff
→ **Handoff 2a** (Backend Implementation) and **Handoff 2b** (Frontend Implementation)

---

## Handoff 2a: Spec → Backend Implementation Agent

**When**: Backend spec and guide are ready

### Input Template

```
Feature: [Feature Name]
Spec: [docs/projects/backend-api/features/{feature}/spec.md]
Backend Guide: [docs/projects/backend-api/features/{feature}/backend-guide.md]

Reference:
- Project Context: [docs/projects/backend-api/context.md]
- Project Memory: [docs/projects/backend-api/memory.md]
- Domain: [docs/domains/backend/context.md]
- Path Instructions: [.github/instructions/paths/]

Tech Stack:
- Framework: [from context.md]
- Database: [from context.md]
- Testing: [from context.md]

Acceptance Criteria: [5-10 testable criteria from spec]
Timeline: [Sprint/Week estimate]
```

### Requirements (Before Implementation Starts)
- [ ] Backend spec is clear and detailed
- [ ] API endpoints defined (method, URL, request/response)
- [ ] Database schema provided (tables, relations)
- [ ] Business logic documented
- [ ] Error cases specified
- [ ] No [CLARIFICATION NEEDED] tags in spec
- [ ] Project tech stack understood

### Success Criteria (When Implementation is Done)
- [ ] All acceptance criteria tested and passing
- [ ] Code follows project patterns (backend.instructions.md)
- [ ] Test coverage > 80% for business logic
- [ ] Error handling complete
- [ ] API documentation updated
- [ ] No breaking changes to existing APIs (or migration plan)
- [ ] Database migrations created (if applicable)
- [ ] Performance targets met (if specified)
- [ ] Security requirements implemented
- [ ] Code review checklist items completed
- [ ] Ready for merge PR

### Quality Gates (Blocking Issues)
- ❌ Tests not passing → Fix before handoff
- ❌ Coverage < 80% → Add more tests
- ❌ Spec not implemented → Complete implementation
- ❌ Security issue found → Fix before handoff
- ❌ Breaking API change → Provide migration
- ❌ Performance degraded → Optimize

### Output Template

```markdown
# Backend Implementation: [Feature Name]

Status: Ready for Review

## Handoff Checklist
- [x] Feature complete
- [x] Tests passing (coverage: X%)
- [x] No breaking changes
- [x] API docs updated
- [x] Security reviewed
- [x] Performance acceptable
- [x] Code review items done

## Pull Request
[Link to PR with detailed description]

## Implementation Summary
- **API Endpoints**: [List]
- **Database Changes**: [List migrations]
- **Key Files**:
  - [src/services/feature.service.ts]
  - [src/handlers/feature.handler.ts]
  - [migrations/...]

## Tests
- Unit tests: [X] passing
- Integration tests: [X] passing
- Coverage: X%

## Acceptance Criteria Met
1. ✅ [Criterion 1 - How verified]
2. ✅ [Criterion 2 - How verified]
... (all 5-10)

## Known Issues / Limitations
- [Any known issues]

## Next Steps
Hand off to Review/QA Agent (after frontend implementation complete).
```

### Next Handoff
→ **Handoff 3** (both frontend + backend to Review Agent)

---

## Handoff 2b: Spec → Frontend Implementation Agent

**When**: Frontend spec and guide are ready

### Input Template

```
Feature: [Feature Name]
Spec: [docs/projects/frontend-web/features/{feature}/spec.md]
Frontend Guide: [docs/projects/frontend-web/features/{feature}/frontend-guide.md]

Reference:
- Project Context: [docs/projects/frontend-web/context.md]
- Project Memory: [docs/projects/frontend-web/memory.md]
- Domain: [docs/domains/frontend/context.md]
- Path Instructions: [.github/instructions/paths/components.instructions.md]

Tech Stack:
- Framework: [from context.md]
- State Management: [from context.md]
- Testing: [from context.md]

UI Requirements: [Mockups / Design specs]
Acceptance Criteria: [5-10 testable criteria from spec]
Timeline: [Sprint/Week estimate]
API Contract: [Endpoint specs from backend guide]
```

### Requirements (Before Implementation Starts)
- [ ] Frontend spec is clear (pages, components, flows)
- [ ] UI mockups or design specs provided
- [ ] State management approach defined
- [ ] API contract finalized (what backend provides)
- [ ] Accessibility requirements specified
- [ ] Responsive design breakpoints defined
- [ ] No [CLARIFICATION NEEDED] tags in spec
- [ ] Project tech stack understood

### Success Criteria (When Implementation is Done)
- [ ] All acceptance criteria tested and passing
- [ ] Code follows project patterns (frontend.instructions.md)
- [ ] Test coverage > 75% for component logic
- [ ] Responsive design verified (mobile/tablet/desktop)
- [ ] Accessibility verified (WCAG 2.1 AA)
- [ ] API integration working (uses backend endpoints)
- [ ] Error states handled
- [ ] Loading states implemented
- [ ] Performance targets met (Lighthouse > 90)
- [ ] Code review checklist items completed
- [ ] Ready for merge PR

### Quality Gates (Blocking Issues)
- ❌ Tests not passing → Fix before handoff
- ❌ Coverage < 75% → Add more tests
- ❌ Accessibility issues → Fix (WCAG violations)
- ❌ Responsive broken → Fix for all breakpoints
- ❌ API integration failing → Debug with backend
- ❌ Lighthouse < 90 → Optimize
- ❌ Spec not implemented → Complete

### Output Template

```markdown
# Frontend Implementation: [Feature Name]

Status: Ready for Review

## Handoff Checklist
- [x] Feature complete
- [x] Tests passing (coverage: X%)
- [x] Responsive design verified
- [x] Accessibility verified (WCAG AA)
- [x] Performance good (Lighthouse X)
- [x] API integration working
- [x] Code review items done

## Pull Request
[Link to PR with detailed description]

## Implementation Summary
- **UI Components**: [List key components]
- **Pages**: [List pages/routes modified]
- **State Management**: [What state added]
- **Key Files**:
  - [src/components/...]
  - [src/pages/...]
  - [src/hooks/...]

## Tests
- Component tests: [X] passing
- Integration tests: [X] passing
- Coverage: X%
- Lighthouse: X/100

## Accessibility Verification
- [ ] Keyboard navigation works
- [ ] Screen reader compatible
- [ ] Color contrast >= 4.5:1
- [ ] Focus indicators visible

## Responsive Design
- [ ] Mobile (< 768px) verified
- [ ] Tablet (768-1024px) verified
- [ ] Desktop (> 1024px) verified

## Acceptance Criteria Met
1. ✅ [Criterion 1 - How verified]
2. ✅ [Criterion 2 - How verified]
... (all 5-10)

## Known Issues / Limitations
- [Any known issues]

## Next Steps
Hand off to Review/QA Agent (after backend implementation complete).
```

### Next Handoff
→ **Handoff 3** (both frontend + backend to Review Agent)

---

## Handoff 3: Implementation → Review/QA Agent

**When**: Both backend and frontend implementations complete

### Input Template

```
Feature: [Feature Name]
Project: [backend-api + frontend-web]

**Feature Spec**: [docs/projects/{project}/features/{feature}/spec.md]
**Acceptance Criteria**: [5-10 criteria]

**Backend Implementation**:
- PR: [Link]
- Coverage: X%
- Status: Ready for review

**Frontend Implementation**:
- PR: [Link]
- Coverage: X%
- Status: Ready for review

**Reference**:
- Original SRS: [docs/projects/{project}/srs/{feature}/SRS.md]
- Backend Context: [docs/projects/backend-api/context.md]
- Frontend Context: [docs/projects/frontend-web/context.md]
```

### Requirements (Before Review Starts)
- [ ] Both PRs created and ready
- [ ] Backend tests passing
- [ ] Frontend tests passing
- [ ] CI/CD checks passing
- [ ] No merge conflicts
- [ ] Deployment preview available (if applicable)

### Success Criteria (When Review is Done)
- [ ] Backend matches backend spec requirements
- [ ] Frontend matches frontend spec requirements
- [ ] All acceptance criteria verified working
- [ ] No security issues
- [ ] No performance regressions
- [ ] Test coverage adequate (80% backend, 75% frontend)
- [ ] API integration working end-to-end
- [ ] Documentation updated
- [ ] Code quality acceptable
- [ ] Ready for approval

### Quality Gates (Blocking Issues)
- ❌ Spec not implemented → Request changes
- ❌ Acceptance criteria not met → Request changes
- ❌ Tests not passing → Reject, fix first
- ❌ Security vulnerability → Reject, fix first
- ❌ Performance degraded → Reject, optimize first
- ❌ Coverage too low → Request more tests
- ❌ API integration broken → Request fix

### Output Template

```markdown
# Review & QA: [Feature Name]

Status: [APPROVED | REQUEST CHANGES]

## Review Checklist
- [x] Backend spec implemented
- [x] Frontend spec implemented
- [x] All acceptance criteria met
- [x] Tests passing
- [x] Coverage adequate
- [x] Security reviewed
- [x] Performance verified
- [x] Documentation updated

## Backend Review
- PR: [#123]
- Coverage: X% ✅
- Tests Status: All passing ✅
- Code Quality: Good ✅
- Security: No issues ✅
- Performance: Acceptable ✅
- Feedback: [Any feedback]

## Frontend Review
- PR: [#124]
- Coverage: X% ✅
- Tests Status: All passing ✅
- Code Quality: Good ✅
- Accessibility: WCAG AA ✅
- Performance: Lighthouse X ✅
- Responsive: All breakpoints OK ✅
- Feedback: [Any feedback]

## Integration Testing
- End-to-end flow: ✅ Working
- API contract: ✅ Correct
- Error handling: ✅ Proper
- State management: ✅ Correct

## Acceptance Criteria Verification
1. ✅ [Criterion 1] — Verified via [method]
2. ✅ [Criterion 2] — Verified via [method]
... (all 5-10)

## Approval Decision

**Status**: APPROVED ✅

Can now be merged and deployed.

---

## If Changes Requested

**Status**: CHANGES REQUESTED

Backend issues:
- [ ] Issue 1 — [Details]
- [ ] Issue 2 — [Details]

Frontend issues:
- [ ] Issue 1 — [Details]
- [ ] Issue 2 — [Details]

Address above and resubmit for review.
```

### Next Handoff
→ **Merge & Deploy** (Human approval)

---

## Handoff Anti-Patterns (What NOT to Do)

❌ **Vague handoff**: "Here's the code, review it"  
✅ **Clear handoff**: "[Handoff template filled] - Ready for review against spec [link]"

❌ **Missing context**: Handing off without spec/requirements  
✅ **Complete context**: All specs, diagrams, and project context linked

❌ **Incomplete work**: "Almost done, figure out the rest"  
✅ **Complete work**: All acceptance criteria met, tests passing

❌ **No success criteria**: "See if it looks good"  
✅ **Clear criteria**: "Verify these 10 acceptance criteria are working"

❌ **Blocking issues ignored**: Merging code with failing tests  
✅ **Quality gates**: Tests must pass before handoff

---

## Escalation Path

If an agent gets stuck:

1. **Document the blocker** in spec with `[BLOCKER]` tag
2. **Link to specific issue** causing the block
3. **Tag for human review**: "Needs clarification"
4. **Pause handoff** until resolved
5. **Update spec** with resolution
6. **Resume** work

Example escalation in spec:

```markdown
## Implementation Guide

### User Authentication
**Backend spec**: Use JWT with 24hr expiry

[BLOCKER] Frontend needs to know: 
- Where is token stored? (localStorage / sessionStorage / httpOnly cookie)?
- What if token expires during operation?
- How to refresh expired token?

Awaiting backend clarification before implementing token handling.
```

---

**Protocol Version**: 1.0  
**Last Updated**: February 23, 2026
