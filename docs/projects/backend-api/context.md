# Backend API Project — Context

This document covers the backend API architecture, tech stack, and conventions specific to this project.

**Last Updated**: February 23, 2026

---

## Project Overview

**Purpose**: Provide REST APIs for all platform services including user management, data operations, and integrations

**Users**: Web frontend, mobile apps, third-party integrations

**Criticality**: Core — Platform dependent on this service

**Deployment**: Single deployment, globally distributed via CDN

---

## Technology Stack

| Layer | Choice | Version | Notes |
|-------|--------|---------|-------|
| **Runtime** | Node.js | 18 LTS | [Node setup guide] |
| **Framework** | Express.js | 4.18 | [Express docs] |
| **Language** | TypeScript | 5.0 | Strict mode enabled |
| **Database** | PostgreSQL | 14 | Primary data store |
| **Cache** | Redis | 7.0 | User sessions, hot data |
| **Testing** | Jest | 29.0 | Unit + integration tests |
| **API Docs** | Swagger/OpenAPI | 3.0 | [API docs endpoint] |
| **Container** | Docker | — | Production deployment |

---

## Architecture

### High-Level Design

```
Requests
    ↓
API Gateway (rate limiting, auth)
    ↓
Express Handlers (validate, dispatch)
    ↓
Services (business logic)
    ↓
Models (database layer)
    ↓
Database / Cache
```

### Key Services

| Service | Purpose | Technology |
|---------|---------|-----------|
| **AuthService** | User auth and tokens | JWT + bcrypt |
| **UserService** | User operations | PostgreSQL |
| **DataService** | Core data operations | PostgreSQL |
| **CacheService** | Performance layer | Redis |
| **EmailService** | Transactional emails | SendGrid API |

---

## Database

### Primary Database

**Type**: PostgreSQL 14

**Key Tables**:
- **users** — Login, profile data
- **posts** — User-generated content
- **tags** — Categorization
- **audit_logs** — Access tracking

**Backup**: Daily automated backups, 30-day retention

**Connection Pool**: Min 2, Max 20 connections

### Naming Conventions

- Tables: `snake_case`, plural (`users`, `user_profiles`)
- Columns: `snake_case` (`created_at`, `is_active`)
- Foreign Keys: `{table_singular}_id` (`user_id`)
- Primary Keys: `id` (UUID auto-generated)

---

## API Design

**Version**: v1

**Base URL**: `https://api.example.com/v1`

**Response Format**:
```json
{
  "data": { /* response payload */ },
  "meta": {
    "timestamp": "2026-02-23T10:30:00Z",
    "version": "1.0"
  },
  "errors": null
}
```

**Error Format**:
```json
{
  "errors": [
    {
      "code": "VALIDATION_ERROR",
      "message": "Email is required",
      "field": "email"
    }
  ]
}
```

**Status Codes**:
- `200` Success
- `201` Created
- `400` Bad Request (validation)
- `401` Unauthorized (auth required)
- `403` Forbidden (permission denied)
- `404` Not Found
- `500` Server Error

**Authentication**: JWT token in `Authorization: Bearer {token}` header

**Rate Limiting**: 100 requests per minute per user, 1000 per hour

---

## Deployment

**Environments**:
- **Development**: Local machine
- **Staging**: [staging-api.example.com]
- **Production**: [api.example.com]

**Deployment Process**:
1. Push to main branch
2. GitHub Actions runs tests and linting
3. If passing, deploy to staging
4. Manual approval to production
5. Health checks verify deployment

**Rollback**: Automated if error rate exceeds 0.5% for 5 minutes

---

## Performance Targets

| Metric | Target | Current |
|--------|--------|---------|
| API Response (p95) | < 200ms | TBD |
| Database Query (p95) | < 100ms | TBD |
| Error Rate | < 0.1% | TBD |
| Uptime | 99.9% | TBD |

---

## Security

**Authentication**: JWT with 24-hour expiry

**Authorization**: RBAC (admin, user, guest)

**Data Protection**: HTTPS only, encrypted at rest in DB

**Input Validation**: All inputs validated with zod schema library

**Secrets**: Environment variables only, never hardcoded

---

## Code Organization

**Directory Structure**:
```
src/
├── handlers/      (route handlers, validation delegation)
├── services/      (business logic, data transformation)
├── models/        (database queries, schema)
├── middleware/    (auth, logging, error handling)
├── errors/        (custom error types)
├── utils/         (helpers, logger)
└── config/        (app configuration)
```

**Documentation**:
- Code: [.github/instructions/backend.instructions.md](../../../.github/instructions/backend.instructions.md)
- Path Specific: [.github/instructions/paths/](../../../.github/instructions/paths/)

---

## Key Decisions

See [memory.md](./memory.md) for specific decisions and trade-offs made for this project.

---

## Useful Commands

```bash
npm start          # Start dev server
npm test           # Run all tests
npm run lint       # Check code style
npm run db:migrate # Run database migrations
npm run build      # Build for production
```

---

**Context Version**: 1.0  
**Last Updated**: February 23, 2026  
**Maintained By**: Backend Team Lead
