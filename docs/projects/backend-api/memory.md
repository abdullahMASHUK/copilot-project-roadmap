# Backend API Project — Memory

This document records backend-specific learnings, decisions, and gotchas discovered through implementation.

**Last Updated**: February 23, 2026

---

## Recent Updates

### 2026-02-23 — Project structure initialized

**Status**: Baseline established

**Details**: Created project-level context and memory files for backend API team

**Impact**: New team members can quickly understand project-specific decisions

---

## Known Issues & Workarounds

### PostgreSQL: Connection Pool Exhaustion

**Symptom**: Application hangs after 30+ concurrent requests

**Root Cause**: Connection pool limited to 10, peak traffic exceeds limit

**Workaround**: Increase pool size in [config/](../../../src/config/), monitor pool usage

**Long-term Fix**: Implement connection pooling proxy (PgBouncer) for better management

**Status**: Monitoring — occurs under unexpected load spikes

---

## Key Decisions

### Decision: JWT for Authentication

**Date**: 2026-01-15

**Context**: Needed stateless auth for distributed API

**Options**:
1. Sessions + Redis
2. **JWT (chosen)**
3. OAuth2

**Why JWT**:
- Stateless (horizontal scaling)
- Reduced redis load
- Standard and widely supported

**Trade-offs**:
- ✅ No session storage needed
- ❌ Token revocation requires blacklist (minor overhead)

---

### Decision: PostgreSQL Primary Database

**Date**: 2026-01-10

**Context**: Highly relational data, needed ACID compliance

**Options**:
1. **PostgreSQL (chosen)**
2. MongoDB
3. MySQL

**Why PostgreSQL**:
- ACID transactions guaranteed
- Complex joins work well
- Team expertise

**Trade-offs**:
- ✅ Data consistency
- ❌ Scaling requires careful sharding strategy

---

## Performance Learnings

### Query Optimization: N+1 Fix

**Pattern**: Batch loading related data instead of per-item queries

**Example**: Fetching 100 users' posts
- Before: 101 queries (1 for users + 100 for posts)
- After: 2 queries (1 for users + 1 for posts with WHERE IN)

**Result**: 50ms → 8ms response time

**Reference**: See [.github/instructions/paths/services.instructions.md](../../../.github/instructions/paths/services.instructions.md#batch-loading-patterns)

---

### Caching: User Profile TTL

**Pattern**: Cache user profiles with 5-minute TTL, invalidate on update

**Impact**: 70% cache hit rate, 5x faster user lookups

**Caution**: Don't cache permission-dependent data (stale permissions are bad)

---

## Testing Patterns

### Learned: Seed Database Properly

**Pattern**: Use fixtures for consistent test data

**Before**: Recreating test users in every test

**After**: Seed once per test suite, clean up after

**Result**: Test suite 30% faster

---

## Team Agreements

### Code Review Checklist (Backend)

- [ ] No hardcoded secrets
- [ ] Input validation on all endpoints
- [ ] Error handling for all paths
- [ ] No N+1 queries (checked visually)
- [ ] Tests pass (unit + integration)

### Release Process

**When**: Every Friday at 3 PM (or on-demand for hotfixes)

**Approval**: Tech lead + one engineer

**Rollback Window**: Up to 1 hour, after that manual assessment required

---

## Metrics

| Metric | Target | Current | Status |
|--------|--------|---------|--------|
| API Response (p95) | < 200ms | TBD | ⏳ |
| Error Rate | < 0.1% | TBD | ⏳ |
| Test Coverage | > 80% | TBD | ⏳ |

---

**Memory Version**: 1.0  
**Last Updated**: February 23, 2026  
**Maintained By**: Backend Team Lead
