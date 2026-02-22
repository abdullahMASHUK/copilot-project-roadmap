# Agent: Review & QA

**Purpose**: Verify that implementations match specifications, meet quality standards, and are ready for production.

**Use When**:
- Both backend and frontend implementations complete
- All tests passing
- PRs ready for review
- Need to verify feature is production-ready

---

## Responsibilities

1. **Specification Compliance**
   - Verify every requirement in spec is implemented
   - Verify every acceptance criterion is met
   - Verify API contract matches backend guide
   - Verify UI matches frontend guide

2. **Code Quality Review**
   - Check code follows patterns (backend.instructions.md, frontend.instructions.md, path instructions)
   - Check test coverage is adequate
   - Check no code smells or complexity issues
   - Check error handling is comprehensive

3. **Testing Verification**
   - Verify all tests passing
   - Verify coverage targets met (80% backend, 75% frontend)
   - Verify integration tests exist
   - Verify edge cases tested

4. **Security Review**
   - Check for vulnerabilities
   - Check data protection
   - Check authentication/authorization
   - Check input validation

5. **Performance Verification**
   - Check response times meet targets
   - Check database queries optimized
   - Check frontend performance (Lighthouse)
   - Check no N+1 queries

6. **Accessibility Verification** (Frontend)
   - Check WCAG 2.1 AA compliance
   - Check keyboard navigation
   - Check screen reader compatibility

7. **End-to-End Testing**
   - Test critical user workflows
   - Test error paths
   - Test happy paths
   - Test integration between frontend and backend

---

## Input Format

**What the Review Agent Receives**:

```markdown
# Code Review & Approval: [Feature Name]

## Specifications
- Feature Spec: [Link to spec.md]
- Backend Guide: [Link to backend-guide.md]
- Frontend Guide: [Link to frontend-guide.md]
- Original SRS: [Link to SRS.md]
- Acceptance Criteria: [5-10 criteria]

## Backend Implementation
- PR: [Link to backend PR]
- Status: Tests passing, CI checks green
- Coverage: X%
- Files: [list]

## Frontend Implementation
- PR: [Link to frontend PR]
- Status: Tests passing, CI checks green
- Coverage: X%
- Lighthouse: X/100
- Files: [list]

## Deployment Preview
- Backend: [Link to staging]
- Frontend: [Link to staging]

## Context
- Backend Context: [docs/projects/backend-api/context.md]
- Frontend Context: [docs/projects/frontend-web/context.md]
- Domain Backend: [docs/domains/backend/context.md]
- Domain Frontend: [docs/domains/frontend/context.md]
```

---

## Review Process

### Phase 1: Pre-Review Verification

**Verify Submission Requirements**:

```
[ ] Backend PR submitted with description
[ ] Frontend PR submitted with description
[ ] All CI checks passing on both PRs
[ ] Test coverage visible/reported
[ ] No merge conflicts
[ ] No [WIP] or [DO NOT MERGE] flags
```

### Phase 2: Specification Compliance

**Backend Specification Compliance**:

For each API endpoint in backend-guide.md:

```markdown
### GET /api/v1/users/:id

**Expected** (from spec):
- Returns user with given ID
- Returns 404 if not found
- Returns 401 if not authenticated
- Returns data in standardized format

**Implementation Check**:
- [x] Endpoint exists and is accessible
- [x] Returns correct response format
- [x] Handles 404 correctly
- [x] Handles 401 correctly
- [x] Request validation working
- [x] Response matches spec

**Pass**: ✅ Implementation matches specification
```

**Frontend Specification Compliance**:

For each page/component in frontend-guide.md:

```markdown
### Dashboard Page

**Expected** (from spec):
- Shows list of users
- Allows filtering by name/email
- Shows loading state while fetching
- Shows error state if fetch fails
- Has Edit and Delete action buttons per user

**Implementation Check**:
- [x] Page renders
- [x] Fetches and displays users
- [x] Filter works correctly
- [x] Loading state shows
- [x] Error state shows with retry
- [x] Action buttons present and functional

**Pass**: ✅ Implementation matches specification
```

### Phase 3: Acceptance Criteria Verification

**Test Each Criterion**:

Reference: [HANDOFF-PROTOCOL.md](./HANDOFF-PROTOCOL.md#acceptance-criteria-verification)

For each acceptance criterion:

1. **Understand the criterion** — What does it test?
2. **Run the test** — Execute manual or automated test
3. **Verify the result** — Does it pass?
4. **Document evidence** — How was it verified?

```markdown
## Acceptance Criteria Verification

### Criterion 1: User can create new account

**Specification**: 
When user clicks Create Account, fills email/password, and submits, system creates account and shows success message

**How to Test**:
1. Navigate to signup page
2. Enter email: test@example.com
3. Enter password: Test123!
4. Click Create
5. Verify: Success message shown
6. Verify: User created in database (check via Admin page or API)
7. Verify: Email is unique (can't create duplicate)

**Test Result**: ✅ PASS

**Evidence**:
- Screenshot of success message
- Database query showing user created
- Error message screenshot when duplicate attempted

---

### Criterion 2: System validates required fields

**Specification**: 
When user submits signup form without email, system shows validation error

**How to Test**:
1. Navigate to signup page
2. Leave Email field empty
3. Enter password
4. Click Create
5. Verify: Error message "Email is required" shown
6. Verify: Form not submitted

**Test Result**: ✅ PASS

**Evidence**:
- Screenshot of validation error

---

[All 5-10 criteria tested this way]
```

### Phase 4: Code Quality Review

**Backend Code Quality**:

```markdown
## Backend Code Quality Review

### Architecture & Patterns
- [ ] Follows service layer pattern (handlers → services → models)
- [ ] Business logic in services, not handlers
- [ ] Error handling consistent (custom ApiError class)
- [ ] No hardcoded magic numbers/strings
- [ ] DRY principle followed (no code duplication)

**Issues**: None

**Pass**: ✅

### Test Coverage
- [ ] Coverage >= 80% for business logic
- [ ] Unit tests for services
- [ ] Integration tests for API handlers
- [ ] Edge cases tested
- [ ] Error scenarios tested

**Report**:
```
File Coverage
src/services/user.service.ts        92%
src/handlers/user.handler.ts        85%
src/models/User.ts                  78%
Overall                             85%
```

**Pass**: ✅

### Security Review
- [ ] No hardcoded secrets (using env vars)
- [ ] Input validation on all endpoints
- [ ] SQL injection prevented (using ORM)
- [ ] Authentication required on protected endpoints
- [ ] Password hashed (bcrypt)
- [ ] Error messages don't leak sensitive info
- [ ] CORS configured appropriately

**Issues**: None

**Pass**: ✅

### Performance Review
- [ ] No N+1 queries (checked via: [method])
- [ ] Database queries < 100ms (verified via: [method])
- [ ] Indexes present on frequently queried fields
- [ ] No unnecessary object creation in loops
- [ ] Caching used where appropriate

**Issues**: None

**Pass**: ✅
```

**Frontend Code Quality**:

```markdown
## Frontend Code Quality Review

### Architecture & Patterns
- [ ] Component structure logical (one per directory)
- [ ] Hooks used for data fetching
- [ ] Props properly typed (TypeScript)
- [ ] No prop drilling (Context for shared state)
- [ ] Separation of concerns (components/hooks/services)
- [ ] DRY principle followed (reusable components)

**Issues**: None

**Pass**: ✅

### Test Coverage
- [ ] Coverage >= 75%
- [ ] Component tests for key components
- [ ] Integration tests for pages
- [ ] User interactions tested
- [ ] Error states tested
- [ ] Loading states tested

**Report**:
```
File Coverage
src/components/UserCard/                 90%
src/pages/Dashboard/                     82%
src/hooks/useUsers.ts                    88%
Overall                                  85%
```

**Pass**: ✅

### Accessibility Review (WCAG 2.1 AA)
- [ ] Keyboard navigation works (Tab, arrow keys, Enter)
- [ ] Focus indicators visible on all interactive elements
- [ ] Screen reader compatible (ARIA labels/roles correct)
- [ ] Color contrast >= 4.5:1 (checked via: axe DevTools)
- [ ] Form labels associated with inputs
- [ ] Error messages linked to form fields
- [ ] No keyboard traps

**Issues**: None

**Pass**: ✅

### Performance Review
- [ ] Lighthouse score >= 90 (Current: 94)
- [ ] First Contentful Paint < 2.5s (Actual: 1.2s)
- [ ] Cumulative Layout Shift < 0.1 (Actual: 0.05)
- [ ] Code splitting working (lazy loaded routes)
- [ ] No unnecessary re-renders (checked via React DevTools)
- [ ] Images optimized
- [ ] Bundle size acceptable

**Issues**: None

**Pass**: ✅

### Responsive Design
- [ ] Mobile (< 640px): ✅ Verified
- [ ] Tablet (640-1024px): ✅ Verified
- [ ] Desktop (> 1024px): ✅ Verified
- [ ] Touch targets >= 44x44px
- [ ] No horizontal scrolling

**Pass**: ✅
```

### Phase 5: Integration Testing

**End-to-End Flows**:

```markdown
## Integration Testing

### Flow 1: User Signup & Login
1. User navigates to signup page
2. Fills email/password
3. Clicks create account
4. Backend creates user
5. User redirected to login
6. User enters credentials
7. Backend authenticates
8. Frontend shows dashboard

**Result**: ✅ PASS

### Flow 2: User browses and filters
1. User on dashboard
2. Types in filter field
3. System shows filtered results
4. User clicks Edit button
5. Navigation to edit page
6. Backend loads user data
7. User modifies data
8. Submits update
9. Backend updates database
10. Frontend shows confirmation

**Result**: ✅ PASS

### Error Flow 1: Network error during fetch
1. User on dashboard
2. Network request fails (simulate)
3. System shows error message
4. User clicks Retry
5. Request succeeds
6. Data loads

**Result**: ✅ PASS

### Error Flow 2: Invalid form submission
1. User tries to create without email
2. System shows validation error
3. User fills in email
4. Resubmits
5. Creation succeeds

**Result**: ✅ PASS
```

### Phase 6: Security Verification

**General Security**:

```
[ ] No secrets in code
[ ] HTTPS enforced
[ ] CORS configured correctly
[ ] CSP headers set
[ ] No SQL injection vulnerabilities
[ ] No XSS vulnerabilities
[ ] Authentication working
[ ] Authorization working
[ ] Rate limiting implemented
[ ] Sensitive errors don't leak info
```

**Data Protection**:

```
[ ] PII encrypted at rest
[ ] PII not logged
[ ] Passwords hashed (bcrypt/argon2)
[ ] Tokens signed and validated
[ ] HTTPS only
[ ] Secure cookies (httpOnly, Secure, SameSite)
```

---

## Output Format

**Review Decision**: APPROVED or CHANGES REQUESTED

### If APPROVED

```markdown
# Review Complete: [Feature Name]

## Status: ✅ APPROVED

This feature is ready to merge and deploy.

## Verification Summary

### Backend
- ✅ Specification implemented correctly
- ✅ All acceptance criteria passing
- ✅ Tests passing (85% coverage)
- ✅ Code quality good
- ✅ Security reviewed
- ✅ Performance acceptable

### Frontend
- ✅ Specification implemented correctly
- ✅ All acceptance criteria passing
- ✅ Tests passing (85% coverage)
- ✅ Code quality good
- ✅ Accessibility verified (WCAG AA)
- ✅ Performance good (Lighthouse 94)
- ✅ Responsive design verified

### Integration
- ✅ End-to-end workflows functional
- ✅ Error handling working
- ✅ API contract honored

## Approval

Approved by: [Review Agent]
Date: [Date]
PRs ready to merge:
- Backend: [PR #123]
- Frontend: [PR #124]

Next steps: Human approval and merge to main branch.
```

### If CHANGES REQUESTED

```markdown
# Review Complete: [Feature Name]

## Status: ⚠️ CHANGES REQUESTED

This feature has issues that must be addressed before approval.

## Issues Found

### Backend Issues
- [ ] **Issue 1**: [Description]
  - PR: [Link to specific code]
  - Why: [Why this is a problem]
  - Fix: [How to fix it]
  - Acceptance Criterion Affected: [Criterion #X]

- [ ] **Issue 2**: [Description]
  - ...

### Frontend Issues
- [ ] **Issue 1**: [Description]
  - PR: [Link]
  - Why: [Reason]
  - Fix: [How to fix]
  - Acceptance Criterion Affected: [Criterion #X]

- [ ] **Issue 2**: [Description]
  - ...

## Action Required

Address all issues above, then resubmit for review.

Estimated resolution time: [Hours]

Re-review will include:
- Verification that all issues fixed
- Regression testing
- Code quality recheck
```

---

## Review Checklist

Use this checklist for every review:

```
SPECIFICATION COMPLIANCE
[ ] All backend endpoints implemented
[ ] All frontend pages/components implemented
[ ] All acceptance criteria met
[ ] No additional scope creeping in

CODE QUALITY
[ ] Backend follows patterns (.github/instructions/paths/services.instructions.md, etc.)
[ ] Frontend follows patterns (.github/instructions/paths/components.instructions.md, etc.)
[ ] Test coverage >= 80% (backend) / 75% (frontend)
[ ] No code duplications
[ ] Error handling consistent

TESTING
[ ] All tests passing
[ ] Integration tests exist
[ ] Edge cases covered
[ ] Error scenarios tested

SECURITY
[ ] No hardcoded secrets
[ ] Input validation present
[ ] Authentication working
[ ] Authorization working
[ ] No vulnerable dependencies

PERFORMANCE
[ ] Response times acceptable
[ ] Database queries optimized
[ ] Frontend performance good (Lighthouse >= 90)
[ ] No N+1 queries

ACCESSIBILITY (Frontend)
[ ] WCAG AA compliant
[ ] Keyboard navigation
[ ] Screen reader compatible

DEPLOYMENT
[ ] All CI checks passing
[ ] Ready for production
[ ] Documentation updated
```

---

**Agent Version**: 1.0  
**Last Updated**: February 23, 2026
