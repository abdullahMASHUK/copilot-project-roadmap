# ADR-001: Context Strategy for Copilot-Assisted Development

**Status**: Accepted

**Date**: February 23, 2026

**Author**: Architecture Team

## Context

The team uses GitHub Copilot for assisted development across multiple domains (backend, frontend, DevOps). Without a structured approach to providing context, Copilot guidance is inconsistent, incomplete, and team members often reinvent solutions instead of following established patterns.

### Problem
- Team members had no standard way to reference project knowledge
- Copilot lacked critical context about decisions, conventions, and gotchas
- Knowledge was dispersed across Slack, old PRs, and individual heads
- New feature specs were incomplete or ambiguous
- Architecture decisions weren't documented, leading to inconsistent patterns

### Goals
1. Create a hierarchical context system that Copilot can reference
2. Establish single sources of truth for architecture, decisions, and patterns
3. Make onboarding faster by centralizing team knowledge
4. Enable reproducible, consistent development workflows
5. Capture learnings continuously to avoid repeating mistakes

## Decision

We adopted a **hierarchical, continuous context system** organized by scope:

### Structure

```
Global Level (workspace-wide)
├── context.md      — Architecture, tech stack, conventions
├── memory.md       — Decisions made, why they were made, constraints
└── changelog.md    — Recent spec/context updates

Domain Level (per-domain: backend, frontend, DevOps)
├── context.md      — Domain-specific tech decisions, patterns
├── memory.md       — Domain-specific gotchas, performance notes
└── (per-feature)
    ├── spec.md     — Requirements, acceptance criteria, APIs
    ├── context.md  — Feature-specific context (optional)
    └── memory.md   — Feature-specific learnings (optional)
```

### Key Principles

1. **Explicit References** — Copilot instructions reference these files by path
2. **Hierarchical Scope** — Global → Domain → Feature (most specific wins)
3. **Continuous Updates** — Memory files updated after every sprint/feature
4. **Single Source of Truth** — Context lives in git, not Slack or email
5. **Timestamped Entries** — Memory entries dated so recency is clear

## Rationale

### Why This Structure?

1. **Scalability** — Works for 1 person or 20 without becoming unwieldy
2. **Flexibility** — Per-app/per-domain/per-feature granularity as needed
3. **Discoverability** — Files live in git, searchable and versioned
4. **Copilot Integration** — Works with explicit instruction references
5. **Team Alignment** — Enforces written decisions, visible to all

### Why Hierarchical?

- Global rules apply everywhere (Don't hardcode secrets)
- Domain rules apply within domain (Use React hooks pattern)
- Feature rules apply to specific feature (This API endpoint is special)
- Most specific context wins if there's conflict

### Why Continuous Memory Updates?

- Lessons from implementation matter more than pre-implementation planning
- Team learns together by capturing discoveries
- Prevents same bugs/gotchas from recurring
- Memory becomes team knowledge base over time

## Alternatives Considered

### Option 1: Centralized Single File
- **Pros**: Simple, everything in one place
- **Cons**: Becomes bloated, hard to find things, merge conflicts

### Option 2: Wiki or External Knowledge Base
- **Pros**: Rich formatting, search capabilities
- **Cons**: Outdated docs, not versioned with code, extra tool

### Option 3: Inline Code Comments Only
- **Pros**: Right next to code, easy to update
- **Cons**: Not discoverable, scattered, incomplete picture

### Option 4: Copilot Chat History
- **Pros**: Natural language, flexible
- **Cons**: Not searchable, lost after session, not version controlled

**Decision**: Our chosen approach combines benefits of inline comments (discoverable, version controlled) with structured organization (hierarchical, readable).

## Consequences

### Positive
- Team has single source of truth for decisions
- Onboarding faster (read 3 docs, understand full context)
- Reproducible development (same context → same patterns)
- Copilot has rich context for better suggestions
- Decisions are documented with rationale
- Learning is captured and shared

### Negative
- Requires discipline to keep files updated
- More files to maintain (17 core files in structure)
- Team must learn new workflow
- Potential for outdated info if not maintained
- Memory files can grow large over time

### Mitigation
- Monthly review of memory files (archive old entries)
- Span process: update context after every feature/sprint
- Automated reminders in PR template
- Periodic cleanup of outdated info

## Implementation Timeline

**Week 1**: 
- Create directory structure
- Write root instructions
- Establish templates

**Week 2**: 
- Fill in domain context files
- Document existing decisions
- Create feature spec template

**Week 3**: 
- Team training on usage
- Create first feature with new system

**Ongoing**: 
- Update memory files continuously
- Archive old decisions annually
- Improve templates based on feedback

## Future Enhancements

1. **Automated Context Discovery** — Tool to suggest which docs to reference
2. **Context Integration in IDE** — VS Code extension showing relevant docs
3. **Version Histories** — Track how decisions evolved over time
4. **Cross-References** — Automatic linking between related docs
5. **Metrics** — Track which context files are most referenced

## Related Decisions

- [ADR-002]: How to structure feature specifications (future)
- [ADR-003]: Memory file maintenance policies (future)

## References

- [docs/_global/context.md](../../docs/_global/context.md) — Current global context
- [docs/_global/memory.md](../../docs/_global/memory.md) — Current decisions log
- [.github/copilot-instructions.md](./../copilot-instructions.md) — Entry point for team

---

**Accepted By**: Tech Lead, Date signed

**Revision History**

| Date | Change |
|------|--------|
| 2026-02-23 | Initial decision documented |
