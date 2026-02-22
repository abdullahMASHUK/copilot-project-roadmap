# Global Memory & Decision History

This document records decisions made, why they were made, and constraints that guide development. Reference this when making decisions or implementing features to avoid repeating past mistakes or reinventing solutions.

**Last Updated**: February 23, 2026

---

## Decision Log

### Decision: Copilot-Assisted Development Workflow (ADR-001)

**Date**: February 23, 2026

**Decision**: Adopted hierarchical context system (global → domain → feature) with explicit references in Copilot instructions.

**Rationale**:
- Ensures consistent guidance across team
- Scalable from 1 person to 20+
- Context lives in git (version controlled, searchable)
- Supports per-app/per-domain/per-feature granularity as needed

**Consequences**:
- Requires discipline to maintain docs
- ~20 files to maintain, but high reusability
- Better for team alignment and onboarding

**Reference**: [.github/decisions/ADR-001-context-strategy.md](../../.github/decisions/ADR-001-context-strategy.md)

---

## Constraints & Non-Negotiables

### Security Constraints

- **No hardcoded secrets** — All sensitive data via environment variables
- **Encrypted in transit** — HTTPS only, no exceptions
- **Authentication required** — Default deny on protected endpoints
- **Input validation** — Validate all user input, never trust client

### Quality Standards

- **Minimum test coverage** — 80% for business logic
- **Code review required** — No merging without approval
- **Linting before merge** — Code must pass linting rules
- **Database migrations** — All schema changes via version-controlled migrations

### Deployment & Operations

- **No manual deployments to production** — All via CI/CD pipeline
- **Rollback plan required** — Before deploying major changes
- **Monitoring alerting** — No going dark on production changes
- **Documentation updated** — Docs kept in sync with code

---

## Lessons Learned

### [Category]: [Lesson Title]

**When**: [Date or circumstance]

**What Happened**: [What went wrong/what we tried]

**Why**: [Root cause]

**Resolution**: [How we fixed it]

**Prevention**: [How we prevent recurrence]

**Status**: [Resolved / Ongoing monitoring]

---

### Performance: Query N+1 Problem in User Dashboard

**When**: Sprint 3 (circa Q1 2026)

**What Happened**: Dashboard load time degraded as user count increased. Investigated and found we were querying user data once per item instead of in bulk.

**Root Cause**: No query optimization during initial feature build. Each item fetched its user data separately (N queries for N items).

**Resolution**: Batch loaded user data with a single query, then mapped results to items.

**Prevention**: Added to code review checklist: "Check for N+1 queries" + performance profiling for data-heavy features.

**Status**: Resolved — Monitoring performance in subsequent features

---

### Development: Feature Spec Ambiguities

**When**: Ongoing issue across sprints

**What Happened**: Developers started on features only to find requirements were unclear, leading to rework.

**Root Cause**: Rushed spec approval without sufficient detail. Specs were vague on edge cases and error handling.

**Resolution**: 
- Created [feature spec template](../../.github/templates/feature-spec.md) with required sections
- Acceptance criteria now must be explicit and testable
- Spec review includes backend + frontend engineer

**Prevention**: 
- Template enforces detail
- Checklist ensures completeness before development starts
- Team trains on spec writing

**Status**: Resolved — New specs follow template, fewer ambiguities

---

### DevOps: Database Migration Rollback Failures

**When**: Q4 2025 production incident

**What Happened**: Production deployment failed, we tried to rollback, but rollback also failed. Service was down for 2 hours.

**Root Cause**: Migrations weren't tested in reverse. We didn't practice rollback before deploying.

**Resolution**:
- Every migration must have a tested rollback
- Staging deployment includes rollback test
- Runbook created for incident response

**Prevention**:
- CI/CD now requires rollback test passing
- Team runs disaster recovery drill quarterly
- Improved monitoring alerts us faster to issues

**Status**: Resolved — Multiple safeguards in place, zero rollback failures since

---

### Team: Context Overcommunication

**When**: Early team expansion (added 2 engineers)

**What Happened**: New engineers made decisions that conflicted with existing conventions. Lots of back-and-forth in code review.

**Root Cause**: Team conventions existed only in tribal knowledge. No written source of truth.

**Resolution**: 
- Documented all conventions [in context files](./context.md)
- Created [instructions per domain](../../.github/instructions/)
- Onboarding includes "read these 3 docs" checklist

**Prevention**:
- New team member onboarding includes context review
- Architecture decisions documented in ADRs
- Memory updated continuously

**Status**: Ongoing — Preventing similar friction as team grows

---

## Technology Decisions

### [Technology Question]: [Decision Made]

**Context**: [Why we needed to decide]

**Options Considered**:
1. [Option A] — [Pros/Cons]
2. [Option B] — [Pros/Cons]
3. [Option C] — [Pros/Cons]

**Decision**: [We chose Option X because...]

**Trade-offs**:
- [Benefit] vs [Cost]
- [Benefit] vs [Cost]

**Revisit**: [When/if we should reconsider]

---

### State Management: Context API vs Redux

**Context**: As frontend complexity grew, we needed centralized state management for user, auth, and app settings.

**Options Considered**:
1. **Redux** — Mature, battle-tested, large boilerplate
2. **Context API** — Simple, built-in, less boilerplate
3. **Zustand** — Lightweight, good DX, smaller community

**Decision**: Context API + custom hooks

**Reasoning**:
- Our state needs are moderate (auth, user, settings)
- Redux boilerplate was overkill
- Zustand required new dependency

**Trade-offs**:
- ✅ Simple, no extra dependencies
- ✅ Good for our current scale
- ❌ May need refactor if complexity grows
- ❌ Manual optimization needed for large states

**Revisit**: If state complexity doubles, evaluate Redux/Zustand again

---

### Database: Choosing PostgreSQL

**Context**: Early project, needed to choose relational database.

**Options Considered**:
1. **PostgreSQL** — Robust, mature, ACID compliance, open source
2. **MySQL** — Faster, simpler, less feature-rich
3. **MongoDB** — Document-based, flexible schema, weaker transactions

**Decision**: PostgreSQL

**Reasoning**:
- Our data model is highly relational (users, posts, comments)
- Need strong consistency (financial data eventually)
- Team experience with PostgreSQL

**Trade-offs**:
- ✅ ACID transactions guaranteed
- ✅ Complex joins work well
- ❌ Schema changes require migrations (good for safety, slower iteration)
- ❌ Scaling requires careful sharding strategy

**Revisit**: If we need to store semi-structured data, may add MongoDB alongside PostgreSQL for specific use cases.

---

## Known Gotchas & Workarounds

### [Component/System]: [Gotcha Name]

**Issue**: [What happens]

**Root Cause**: [Why it happens]

**Workaround**: [How to handle it]

**Long-term Fix**: [If applicable, what should we do]

**Status**: [Resolved / Monitoring / No-plan-yet]

---

### Frontend: React StrictMode Double-Rendering in Development

**Issue**: Components render twice in development, once in production. Causes confusing logs and unexpected behavior.

**Root Cause**: React 18 StrictMode intentionally double-renders to detect bugs in useEffect.

**Workaround**: Expected behavior. Check that effects are idempotent (safe to run twice).

**Long-term Fix**: None — this is by design.

**Status**: Resolved — Documented in component patterns

---

### Backend: PostgreSQL Concurrent Update Deadlocks

**Issue**: Under high concurrency, concurrent updates to same row cause deadlocks ["ERROR: deadlock detected"].

**Root Cause**: Transaction isolation and lock ordering when multiple transactions update same row.

**Workaround**: 
- Use explicit row-level locks: `SELECT ... FOR UPDATE`
- Order locks consistently across transactions
- Keep transactions short

**Long-term Fix**: Evaluate optimistic locking pattern if deadlocks increase.

**Status**: Monitoring — Rare in current load, but documented for future scaling

---

### DevOps: GitHub Actions Timeout on Large Builds

**Issue**: Backend build times > 10 minutes, sometimes timeout in CI.

**Root Cause**: Large dependency tree and slow build on shared runners.

**Workaround**: 
- Cache node_modules aggressively
- Parallelize tests
- Use larger runner for this job

**Long-term Fix**: Consider dedicated runner or pre-built images.

**Status**: Monitoring — Works for now, revisit if builds get slower

---

## Team Agreements & Conventions

### Communication

- **Daily standup**: 10 AM, 15 minutes max
- **Code review SLA**: 24 hours (or next business day)
- **Slack response**: Best effort, not guaranteed to be immediate
- **Decisions**: Documented in ADRs, not Slack threads

### Release Schedule

- **Sprints**: 2 weeks
- **Release Day**: Every Friday at 3 PM
- **Hotfixes**: As needed, approved by on-call engineer
- **Rollback Window**: Up to 1 hour, then manual assessment

### On-Call Rotation

- **Coverage**: 24/7 rotation (primary + backup)
- **Response Time**: 15 minutes for critical, 1 hour for high
- **Escalation**: If can't resolve in 30 min, reach out for help

---

## Metrics to Track

| Metric | Target | Current | Owner | Review |
|--------|--------|---------|-------|--------|
| Test Coverage | > 80% | TBD | QA | Monthly |
| API Latency (p95) | < 200ms | TBD | Backend | Weekly |
| Page Load Time | < 2s | TBD | Frontend | Weekly |
| Incident Response Time | < 15 min | TBD | DevOps | Monthly |
| Code Review Cycle | < 24h | TBD | Tech Lead | Monthly |

---

## Future Considerations

### Technology Roadmap

- **Q2 2026**: [Planned upgrade / experiment]
- **Q3 2026**: [Planned upgrade / experiment]
- **2027**: [Long-term vision]

### Scaling Plans

- **Next 10x users**: [Infrastructure changes needed]
- **1M+ users**: [Architectural changes needed]

### Team Growth

- **Q2 2026**: Add 2 backend engineers
- **Q3 2026**: Add designer and QA engineer

---

## Questions & Clarifications

**Q: When should we escalate a decision to the team?**

A: If it affects multiple people or domains, or reverses a past decision, discuss with tech lead and domain leads before implementing.

**Q: How do we update this document?**

A: After every sprint, review learnings. After every significant decision, document in appropriate section. Make a PR, get review, merge.

---

**Memory Version**: 1.0  
**Last Updated**: February 23, 2026  
**Maintained By**: [Tech Lead]
