# Global Context

This document describes the high-level architecture, technology stack, and project-wide conventions. Reference this when making architectural decisions or understanding the overall system design.

**Last Updated**: February 23, 2026

---

## Project Overview

**[Project Name]**

[1-2 sentence description of what the project does]

**Team**: [Team name/members]

**Repositories**:
- `[main repo]` — [Description]
- `[related repo]` — [Description]

---

## Architecture Overview

### System Design

[High-level diagram or description of how components interact]

**Key Components**:
- **Frontend** — [Technology, purpose]
- **Backend** — [Technology, purpose]
- **Database** — [Type, structure]
- **External Services** — [Third-party integrations]

### Technology Stack

| Layer | Technology | Version | Notes |
|-------|-----------|---------|-------|
| **Deployment** | [Docker / Kubernetes / Other] | [version] | [Notes] |
| **Backend Runtime** | [Node.js / Python / Java / Other] | [version] | [Notes] |
| **Backend Framework** | [Express / Django / Spring / Other] | [version] | [Notes] |
| **Database** | [PostgreSQL / MongoDB / Other] | [version] | [Notes] |
| **Frontend Framework** | [React / Vue / Angular / Other] | [version] | [Notes] |
| **Frontend Build** | [Webpack / Vite / Next.js / Other] | [version] | [Notes] |
| **Testing** | [Jest / Pytest / Mocha / Other] | [version] | [Notes] |
| **CI/CD** | [GitHub Actions / Jenkins / Other] | [version] | [Notes] |

### Data Flow

[Describe how data moves through the system]

```
User → Frontend → API → Backend → Database
  ↓                                    ↓
  └────── Cache Layer ─────────────────┘
```

---

## Development Environments

### Local Development

**Setup**: [Quick start instructions]

**Tools Required**:
- [Node.js version]
- [Python version]
- [Database setup]
- [Other dependencies]

**Start Commands**:
```bash
# Backend
npm install && npm run dev

# Frontend
npm install && npm start
```

### Staging

**Purpose**: Test before production

**Deployment**: [How staging is deployed]

**Access**: [Who can access, how]

### Production

**Purpose**: Live service for users

**Deployment**: [How production is deployed]

**Rollback**: [Process for rolling back changes]

**Monitoring**: [Alerts, dashboards, logging]

---

## Code Organization

### Directory Structure

```
/
├── backend/              # Backend code
│   ├── src/
│   ├── tests/
│   └── migrations/       # Database migrations
├── frontend/             # Frontend code
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   └── services/
│   └── tests/
├── .github/
│   ├── workflows/        # CI/CD pipelines
│   ├── instructions/     # Domain-specific guidelines
│   └── prompts/          # Reusable Copilot prompts
├── docs/                 # Project documentation
└── README.md
```

### Naming Conventions

**Files**:
- Components: `PascalCase.tsx` / `PascalCase.jsx`
- Utilities: `camelCase.ts` / `camelCase.js`
- Config: `kebab-case.js` / `kebab-case.ts`

**Functions & Variables**:
- Functions: `camelCase()`
- Classes: `PascalCase`
- Constants: `UPPER_SNAKE_CASE`

**Branches**:
- Feature: `feature/short-description`
- Bugfix: `bugfix/short-description`
- Release: `release/version-number`

---

## Key Conventions

### Code Quality Standards

- **Language**: [TypeScript / JavaScript / Python / Other] is preferred
- **Linting**: [ESLint / Pylint / Other] — Run before committing
- **Formatting**: [Prettier / Black / Other] — Auto-formatted on save
- **Type Safety**: [Strict mode enabled / Some strictness / Loose]
- **Test Coverage**: Minimum 80% for business logic

### Git Workflow

1. **Branch** — Create feature branch from `main`
2. **Develop** — Commit small, descriptive commits
3. **Test** — All tests pass locally
4. **PR** — Create pull request with description
5. **Review** — At least one approval required
6. **Merge** — Squash and merge to maintain history

### API Standards

**REST Conventions**:
- `GET /resource` — List resources
- `POST /resource` — Create resource
- `GET /resource/:id` — Get single resource
- `PUT /resource/:id` — Update resource
- `DELETE /resource/:id` — Delete resource

**Response Format**:
```json
{
  "data": {...},
  "meta": {
    "timestamp": "ISO-8601",
    "version": "1.0"
  },
  "errors": [{"code": "ERROR_CODE", "message": "..."}]
}
```

**Status Codes**:
- `200` — Success
- `201` — Created
- `400` — Bad Request
- `401` — Unauthorized
- `403` — Forbidden
- `404` — Not Found
- `500` — Server Error

### Database Standards

**Migrations**:
- All schema changes via migrations
- Migrations are idempotent
- Rollback tested before merge

**Naming**:
- Tables: `snake_case`, plural (e.g., `users`, `user_profiles`)
- Columns: `snake_case` (e.g., `created_at`, `is_active`)
- Primary keys: `id`
- Foreign keys: `{table_singular}_id` (e.g., `user_id`)

---

## Security & Compliance

### Authentication

- **Method**: [JWT / OAuth / Sessions / Other]
- **Token Expiration**: [Duration]
- **Refresh Strategy**: [How tokens are refreshed]

### Authorization

- **Model**: [RBAC / ABAC / Other]
- **Default**: Deny unless explicitly allowed
- **Audit**: All permission changes logged

### Data Protection

- **Encryption in Transit**: HTTPS only
- **Encryption at Rest**: [Yes / No] — [Where]
- **PII Handling**: [Policy for sensitive data]
- **Data Retention**: [Policy and duration]

### Compliance

- **Standards**: [GDPR / CCPA / SOC2 / Other]
- **Audits**: [Frequency and process]
- **Certifications**: [Any relevant certs]

---

## Performance & Scalability

### Performance Targets

| Metric | Target | Tool |
|--------|--------|------|
| Page Load Time | < 2s | Lighthouse |
| API Response Times | < 200ms p95 | DataDog / CloudWatch |
| Database Query Time | < 100ms p95 | Query profiler |
| Error Rate | < 0.1% | Sentry |

### Scalability

**Current Capacity**:
- Concurrent users: [Number]
- Requests per second: [Number]
- Data storage: [GBs]

**Scaling Strategy**:
- Vertical scaling: [Yes / No]
- Horizontal scaling: [Load balancer, database replication, etc.]
- Caching: [Strategy and key points]

---

## Monitoring & Observability

### Logging

- **Format**: JSON
- **Level**: [DEBUG in dev, INFO in prod]
- **Retention**: [Duration stored]
- **Tool**: [CloudWatch / DataDog / Splunk / Other]

### Metrics

- **Tool**: [Prometheus / DataDog / CloudWatch / Other]
- **Key Metrics**: [List of important metrics]
- **Dashboards**: [Links to key dashboards]

### Alerting

- **Tool**: [PagerDuty / OpsGenie / Slack / Other]
- **On-Call**: [Schedule and process]
- **Runbooks**: [Links to incident response guides]

---

## External Integrations

| Service | Purpose | Status | Contact |
|---------|---------|--------|---------|
| [Service Name] | [What it does] | Active | [Owner contact] |

---

## Resources & Documentation

- **Architecture Decisions**: [.github/decisions/](../.github/decisions/)
- **Domain Guides**: [Backend](./domains/backend/) • [Frontend](./domains/frontend/)
- **Feature Specs**: [Features](./features/)
- **Decision History**: [docs/_global/memory.md](./memory.md)
- **Changelog**: [docs/changelog.md](./changelog.md)

---

## Questions?

- **Architecture question**: Check [.github/decisions/](../.github/decisions/) for similar decisions
- **Tech stack question**: Search this document or [memory.md](./memory.md)
- **Feature planning**: Use [.github/templates/feature-spec.md](../.github/templates/feature-spec.md) template

---

**Context Version**: 1.0  
**Last Updated**: February 23, 2026  
**Maintained By**: [Team]
