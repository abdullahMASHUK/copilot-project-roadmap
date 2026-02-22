# SRS Input Template

Use this template when starting a new feature. Fill it out completely before handing off to Project Planner Agent.

---

# SRS: [Feature Name]

**Date Created**: [Date]  
**Project**: [backend-api | frontend-web | other]  
**Priority**: [Critical | High | Medium | Low]  
**Epic**: [If part of larger work]  

---

## Overview

### Problem Statement

[What problem does this feature solve? Who has the problem? Why is it important?]

### Success Criteria (Business Level)

- [ ] [Business goal 1]
- [ ] [Business goal 2]
- [ ] [Business goal 3]

**Example**:
- [ ] Reduce user signup time from 5 minutes to 1 minute
- [ ] Increase conversion rate to 25%
- [ ] Maintain < 5% error rate

---

## User Stories

### User Story 1: [Story Title]

```gherkin
As a [user role],
I want to [action],
So that [benefit]

Given [context],
When [user action],
Then [expected result]
```

**Example**:
```gherkin
As a new user,
I want to sign up using email,
So that I can access the platform

Given I am on the signup page,
When I enter email and password and click Create,
Then my account is created and I see a confirmation message
```

### User Story 2: [Story Title]

[Repeat above]

---

## Functional Requirements

### Core Features

**Feature 1: [Name]**
- [ ] Requirement 1.1
- [ ] Requirement 1.2
- [ ] Requirement 1.3

**Example**: User Authentication
- [ ] Users can sign up with email/password
- [ ] Users can log in with email/password
- [ ] Passwords must be at least 8 characters
- [ ] Session expires after 24 hours of inactivity

**Feature 2: [Name]**
- [ ] Requirement 2.1
- [ ] Requirement 2.2

### User Workflows

**Happy Path: [Workflow Name]**

1. User [action]
2. System [response]
3. User [action]
4. System [response]
...
Result: [Outcome]

**Example**: User Signup
1. User navigates to signup page
2. System shows signup form
3. User enters email
4. User enters password
5. User clicks Create Account
6. System validates input
7. System creates user in database
8. System sends confirmation email
9. User sees success message

---

## Non-Functional Requirements

### Performance

- **Response Time**: [Target, e.g., < 200ms for p95]
- **Database Query Time**: [Target, e.g., < 100ms for p95]
- **Throughput**: [Requests per second target]
- **Concurrent Users**: [Expected concurrent users]

**Example**:
- API responses < 200ms (p95)
- Database queries < 100ms
- Handle 100 concurrent users
- 99.9% uptime target

### Security

- **Authentication**: [Approach, e.g., JWT, OAuth]
- **Authorization**: [Approach, e.g., RBAC, ABAC]
- **Data Encryption**: [At rest? In transit?]
- **Compliance**: [GDPR, HIPAA, etc.]
- **Password Policy**: [Requirements]

**Example**:
- JWT authentication with 24-hour expiry
- RBAC (Admin, User, Guest roles)
- All data encrypted in transit (HTTPS)
- All passwords hashed with bcrypt
- GDPR compliant (user data can be exported/deleted)

### Scalability

- **Concurrent Users**: [Expected growth]
- **Data Size**: [Expected data growth]
- **Deployment**: [Scaling strategy]

**Example**:
- Current: 1,000 concurrent users
- Target (6 months): 10,000 concurrent users
- Strategy: Horizontal scaling with load balancer

### Accessibility

- **Standard**: [WCAG 2.1 Level AA]
- **Keyboard Navigation**: [Yes/No]
- **Screen Reader**: [Yes/No]
- **Color Contrast**: [4.5:1 minimum]

---

## Data Requirements

### Data Entities

**Entity 1: [Name]**
- id (UUID, primary key)
- [Field 1] (type, constraints)
- [Field 2] (type, constraints)
- created_at (timestamp)
- updated_at (timestamp)

**Example**: User
- id (UUID, primary key)
- email (string, unique, not null)
- password_hash (string, not null)
- name (string)
- role (enum: admin, user, guest)
- is_active (boolean)
- created_at (timestamp)
- updated_at (timestamp)

**Entity 2: [Name]**
- [Fields...]

### Relationships

- [Entity 1] **has many** [Entity 2]
- [Entity 2] **belongs to** [Entity 1]

**Example**:
- User **has many** Posts
- Post **belongs to** User

### Data Storage

- **Primary Database**: [PostgreSQL, MongoDB, etc.]
- **Cache**: [Redis, Memcached, etc.]
- **Search**: [Elasticsearch, etc.]
- **File Storage**: [S3, Blob, etc.]

---

## API Specification (if applicable)

### Endpoints

**Endpoint 1: User Signup**
- **Method**: POST
- **URL**: /api/v1/users
- **Request Body**:
  ```json
  {
    "email": "user@example.com",
    "password": "password123",
    "name": "User Name"
  }
  ```
- **Response** (201 Created):
  ```json
  {
    "data": {
      "id": "uuid",
      "email": "user@example.com",
      "name": "User Name"
    },
    "meta": {"timestamp": "ISO-8601"}
  }
  ```
- **Error** (409 Conflict if email exists):
  ```json
  {
    "errors": [{"code": "EMAIL_EXISTS", "message": "Email already in use"}]
  }
  ```

**Endpoint 2: [Name]**
- [Repeat above]

---

## UI/UX Requirements (if applicable)

### Pages Needed

**Page 1: Signup**
- [Component 1]
- [Component 2]
- [User flows]

**Example**: Signup Page
- Header with logo
- Form with email/password fields
- Submit button
- Link to login page
- Error messages for invalid input
- Loading state during submission
- Success confirmation message

**Page 2: [Name]**
- [Components...]

### Design Specifications

- **Color Scheme**: [Colors or reference to design system]
- **Typography**: [Font families, sizes]
- **Layout**: [Responsive breakpoints]
- **Mockups**: [Link to Figma, Adobe XD, etc.]

---

## Constraints & Assumptions

### Constraints

- [ ] [Technical constraint]
- [ ] [Business constraint]
- [ ] [Timeline constraint]

**Example**:
- [ ] Must be GDPR compliant
- [ ] Cannot modify existing User table structure (backward compatibility)
- [ ] Must complete within 2 sprints
- [ ] Must use existing authentication library

### Assumptions

- [ ] [Assumption]
- [ ] [Assumption]

**Example**:
- [ ] All users will have valid email addresses
- [ ] Passwords will be transmitted over HTTPS only
- [ ] Database backup/recovery is handled separately

### Risks

- **Risk 1**: [Description]
  - **Impact**: [High/Medium/Low]
  - **Mitigation**: [Plan to address]

**Example**:
- **Risk**: High volume of signup requests could overwhelm database
  - **Impact**: High (users can't sign up)
  - **Mitigation**: Implement rate limiting and connection pooling

---

## External Dependencies

- [ ] [Service/System 1] — [What it provides]
- [ ] [Service/System 2] — [What it provides]

**Example**:
- [ ] SendGrid email service — Sends confirmation emails
- [ ] Stripe payment provider — Processes payments

---

## Acceptance Criteria (Definition of Done)

Feature is considered complete when:

**Backend**:
- [ ] All API endpoints implemented
- [ ] Database migrations created
- [ ] Tests: 80%+ coverage
- [ ] Code review approved
- [ ] No breaking changes (or migration provided)

**Frontend**:
- [ ] All pages/components implemented
- [ ] Tests: 75%+ coverage
- [ ] Responsive design verified (mobile/tablet/desktop)
- [ ] Accessibility verified (WCAG AA)
- [ ] Lighthouse score > 90
- [ ] Code review approved

**Integration**:
- [ ] End-to-end workflows working
- [ ] Error handling working
- [ ] Performance targets met
- [ ] Security review passed
- [ ] Documentation updated

---

## Timeline & Effort Estimate

- **Estimated Effort**: [Hours/Days/Sprints]
- **Deadlines**: [Key dates]
- **Dependencies on Other Work**: [Other features/tasks]

---

## References & Attachments

- [ ] [Design mockups](link)
- [ ] [Competitive analysis](link)
- [ ] [Performance targets](link)
- [ ] [Related documentation](link)

---

## Approval

**Product Owner**: [Name] — [ ] Approved [ ] Needs Changes

**Tech Lead**: [Name] — [ ] Approved [ ] Needs Clarification

---

**SRS Version**: 1.0  
**Last Updated**: [Date]
