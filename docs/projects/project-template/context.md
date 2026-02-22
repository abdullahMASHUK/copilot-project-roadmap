# Project: [Project Name] — Context

This document describes this project's architecture, technology stack, and conventions. Read this to understand how this project is built and what patterns to follow.

**Last Updated**: February 23, 2026

---

## Project Overview

**Purpose**: [What this project does and why it matters]

**Users**: [Who uses this project]

**Criticality**: [Core / Important / Optional]

**Deployment**: [SaaS / On-premise / Hybrid / etc.]

---

## Architecture Overview

### High-Level Design

[Describe the main components and how they interact]

### System Diagram

```
[User] → [Frontend] → [API] → [Database]
                 ↓
            [Cache Layer]
```

### Key Services

| Service | Purpose | Technology | Owner |
|---------|---------|-----------|-------|
| [Service Name] | [What it does] | [Tech] | [Owner] |

---

## Technology Stack

| Layer | Technology | Version | Notes |
|-------|-----------|---------|-------|
| **Language** | [e.g., TypeScript, Python] | [version] | [Notes] |
| **Framework** | [e.g., Express, Django] | [version] | [Notes] |
| **Database** | [PostgreSQL, MongoDB, etc.] | [version] | [Schema location] |
| **Cache** | [Redis, Memcached, etc.] | [version] | [When used] |
| **Messaging** | [RabbitMQ, Kafka, etc.] | [version] | [For what] |
| **ORM/Query** | [Prisma, SQLAlchemy, etc.] | [version] | [Usage] |
| **Testing** | [Jest, Pytest, etc.] | [version] | [Unit + integration] |
| **Deployment** | [Docker, K8s, etc.] | [version] | [Infrastructure] |

---

## Architecture Decisions

### API Design

**Approach**: [REST / GraphQL / gRPC / etc.]

**Base URL**: [https://example.com/v1]

**Response Format**:
```json
{
  "data": {...},
  "meta": {"timestamp": "ISO-8601"},
  "errors": null
}
```

**Error Format**:
```json
{
  "errors": [{"code": "ERROR_CODE", "message": "..."}]
}
```

**Authentication**: [JWT / OAuth / Sessions / etc.]

**Rate Limiting**: [Requests per minute / hour]

### Database Design

**Primary Database**: [PostgreSQL / MongoDB / etc.]

**Key Tables/Collections**:
- [Table 1] — [Purpose]
- [Table 2] — [Purpose]

**Naming Conventions**:
- Tables: `snake_case`, plural
- Columns: `snake_case`
- Foreign Keys: `{table_singular}_id`

**Backup Strategy**: [How data is backed up and recovered]

### Caching Strategy

**Cache Layer**: [Redis / In-Memory / etc.]

**What's Cached**:
- [Resource 1] with TTL of [duration]
- [Resource 2] with TTL of [duration]

**Invalidation Strategy**: [Time-based / Event-based / Manual]

### Deployment Architecture

**Environment**: [Development / Staging / Production]

**Hosting**: [Cloud provider, region, etc.]

**Scaling**: [Auto-scaling config, capacity planning]

**Monitoring**: [Tools and dashboards]

---

## Development Environment

### Local Setup

[Commands to get this project running locally]

```bash
# Clone and setup
git clone [repo-url]
cd [project-dir]
npm install
npm run db:migrate
npm run dev
```

### Required Tools

- [Node.js version]
- [Database version]
- [Other tools]

### Environment Variables

| Variable | Purpose | Example |
|----------|---------|---------|
| `DATABASE_URL` | Database connection | `postgres://...` |
| `REDIS_URL` | Cache connection | `redis://...` |
| `API_KEY` | Third-party service | `sk-abc123...` |

### Key Commands

| Command | What It Does |
|---------|------------|
| `npm start` | Start dev server |
| `npm test` | Run tests |
| `npm run db:migrate` | Run migrations |
| `npm run build` | Build for production |
| `npm run lint` | Lint code |

---

## Code Organization

### Directory Structure

```
src/
├── handlers/     (API endpoints)
├── services/     (business logic)
├── models/       (data models)
├── middleware/   (middleware)
├── utils/        (utilities)
├── config/       (configuration)
└── tests/        (tests)
```

### Design Patterns Used

- **Service Layer Pattern**: Business logic in services, handlers delegate
- **Dependency Injection**: Dependencies passed to functions/classes
- **Error Handling**: Custom error types with meaningful messages
- **Logging**: Structured logging with context

### Code Quality Standards

- **Language**: [TypeScript / Python / etc.] with strict checking
- **Linting**: [ESLint / Pylint / etc.] — auto-fixed on save
- **Formatting**: [Prettier / Black / etc.] — enforced in CI
- **Test Coverage**: Minimum 80% for business logic
- **Code Review**: Approval required before merge

---

## Performance Requirements

### SLOs (Service Level Objectives)

| Metric | Target |
|--------|--------|
| API Response Time (p95) | < 200ms |
| Database Query (p95) | < 100ms |
| Error Rate | < 0.1% |
| Uptime | 99.9% |

### Capacity Planning

**Current**: [X requests/second, Y concurrent users]

**Target (6 months)**: [X requests/second, Y concurrent users]

**Scaling Strategy**: [How we'll handle growth]

---

## Security & Compliance

### Authentication

**Method**: [JWT / OAuth / etc.]

**Token Expiration**: [Duration]

**Session Management**: [How sessions work]

### Authorization

**Model**: [RBAC / ABAC / etc.]

**Default**: Deny unless explicitly allowed

### Data Protection

**Encryption in Transit**: HTTPS only

**Encryption at Rest**: [Yes/No] — [Where]

**PII Handling**: [Policy]

**Data Retention**: [Duration]

### Compliance

**Standards**: [GDPR / CCPA / SOC2 / etc.]

**Audits**: [Frequency]

---

## Testing Strategy

### Unit Tests

**Framework**: [Jest / Pytest / etc.]

**Coverage**: 80%+ for business logic

**Pattern**: Arrange-Act-Assert

### Integration Tests

**Scope**: Test workflows with real database

**Database**: Use test database (reset per test)

### E2E Tests

**Tool**: [Cypress / Playwright / etc.]

**Scope**: Critical user workflows

---

## Monitoring & Observability

### Logging

**Level**: [DEBUG / INFO / etc.]

**Format**: JSON structured logs

**Tool**: [CloudWatch / DataDog / etc.]

**Retention**: [Duration]

### Metrics

**Tool**: [Prometheus / DataDog / etc.]

**Key Metrics**:
- Request latency
- Error rate
- Database connections
- Cache hit rate

### Alerting

**Tool**: [PagerDuty / OpsGenie / etc.]

**Critical Alerts**:
- Error rate > 0.5%
- Response time > 500ms
- Database down

---

## Production Runbooks

### Deployment Process

[Step-by-step deployment instructions]

### Incident Response

**On-Call**: [Schedule and escalation]

**Critical Issue**: [Response time required]

**Rollback**: [How to rollback bad deployment]

### Common Issues

[Link to memory.md for known issues and fixes]

---

## External Integrations

| Service | Purpose | Status | Docs |
|---------|---------|--------|------|
| [Service Name] | [What it does] | [Active/Inactive] | [Link] |

---

## Resources & Links

- **Source Code**: [GitHub/GitLab link]
- **Architecture Diagram**: [Link or embed]
- **Database Schema**: [Link to schema docs]
- **API Documentation**: [Swagger/Postman link]
- **Deployment Docs**: [Link to runbooks]
- **Memory & Learnings**: [memory.md](./memory.md)
- **Feature Specs**: [features/](./features/)

---

**Context Version**: 1.0  
**Last Updated**: February 23, 2026  
**Maintained By**: [Team Lead]
