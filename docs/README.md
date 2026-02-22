# Project Documentation

Welcome to the documentation structure for this project. This directory contains context, memory, and specifications that guide all development work with Copilot.

## Directory Overview

### `_global/`
**Workspace-wide context and decisions**
- `context.md` — Project architecture, tech stack, conventions
- `memory.md` — Major decisions, constraints, lessons learned

### `domains/`
**Domain-specific patterns and guidelines**
- `backend/context.md` — Backend architecture, API standards
- `backend/memory.md` — Backend-specific gotchas and optimizations
- `frontend/context.md` — Frontend framework, component patterns
- `frontend/memory.md` — Frontend browser issues, performance notes

### `features/`
**Feature specifications and guides**
- `{feature-name}/spec.md` — Feature requirements and acceptance criteria
- `{feature-name}/context.md` — Feature-specific decisions (optional)
- `{feature-name}/memory.md` — Feature-specific learnings (optional)

### `archive/`
**Historical and deprecated documentation**
- Old feature specs
- Deprecated patterns
- Historical decisions (kept for reference)

## How to Use

### For Development
1. **Start here**: [_global/context.md](_global/context.md) — Understand overall architecture
2. **Then domain**: [domains/{your-domain}/context.md](domains) — Learn domain patterns
3. **For your feature**: [features/{feature-name}/spec.md](features) — Get detailed requirements

### For Decision Making
1. Check [_global/memory.md](_global/memory.md) for similar past decisions
2. Check domain memory for domain-specific concerns
3. Propose new decision with full context

### For Onboarding
1. Read [_global/context.md](_global/context.md)
2. Read domain-specific context for your role
3. Review a completed feature spec
4. Ask questions in the team channel

## Key Principles

- **Context is versioned** — All docs in git, changes tracked
- **Memory is continuous** — Updated after every feature/sprint
- **Explicit over implicit** — Patterns are documented, not assumed
- **Hierarchy matters** — Global→Domain→Feature (most specific wins)
- **Dated entries** — Memory entries timestamped for recency

## Quick Links

- **Onboarding**: [../.github/COPILOT-USAGE.md](../.github/COPILOT-USAGE.md)
- **Instructions**: [../.github/instructions/](../.github/instructions/)
- **Prompts**: [../.github/prompts/](../.github/prompts/)
- **Agents**: [../.github/agents/](../.github/agents/)
- **Decisions**: [../.github/decisions/](../.github/decisions/)

---

**Documentation Version**: 1.0  
**Last Updated**: February 23, 2026
