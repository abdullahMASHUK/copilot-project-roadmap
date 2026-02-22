# Project vs Domain Context: Quick Guide

This document clarifies when to use **project-specific** vs **domain-specific** context, since both exist when working in a multi-project workspace.

---

## The Distinction

### Global Context (`docs/_global/`)
**For**: Company-wide decisions that apply everywhere

**Examples**:
- Security standards (GDPR, encryption requirements)
- Code quality targets (test coverage %)
- Deployment infrastructure (cloud provider)
- Team agreements (code review process)

**Use**: When decision applies to ALL projects

---

### Project Context (`docs/projects/{project}/`)
**For**: Decisions specific to ONE project (tech choices, architecture)

**Examples** (backend-api project):
- Express.js as framework (vs FastAPI in another project)
- PostgreSQL as primary database
- JWT authentication
- RESTful API design

**Examples** (frontend-web project):
- React 18 as framework
- Tailwind CSS for styling
- Vite as build tool
- Context API for state management

**Use**: When a project chose a specific technology, architecture, or pattern

---

### Domain Context (`docs/domains/{backend|frontend}/`)
**For**: Best practices and conventions common across a role

**Examples** (Backend domain):
- Service layer architecture patterns
- Error handling conventions
- API design patterns (shared across backend-api and any other backend projects)
- Database query optimization patterns

**Examples** (Frontend domain):
- React component patterns
- Testing with React Testing Library
- State management approaches
- Accessibility standards

**Use**: When building or reviewing code of a specific type (backend/frontend)

---

## Decision Tree

**When making a decision, ask:**

```
Does this apply to:

1. ALL projects globally?
   → Use: Global Context (docs/_global/)
   
2. ONE specific project?
   → Use: Project Context (docs/projects/{project}/)
   
3. All backends OR all frontends (regardless of project)?
   → Use: Domain Context (docs/domains/{backend|frontend}/)
   
4. Files in a specific directory (src/handlers/, src/components/)?
   → Use: Path Context (.github/instructions/paths/)
   
5. A specific feature being built?
   → Use: Feature Context (docs/features/ or docs/projects/{project}/features/)
```

---

## Real-World Examples

### Example 1: Choosing a Testing Framework

**Question**: Should we use Jest or Vitest?

**Answer**: This is project-specific
- **backend-api**: Uses Jest (Node.js/Express context)
- **frontend-web**: Uses Vitest (React/Vite context)

**Where to document**: Each `docs/projects/{project}/context.md`

**Why not global**: Different project = different needs

**Why not domain**: Testing framework is technology choice, not architectural pattern

---

### Example 2: Component File Organization

**Question**: How should React components be organized?

**Answer**: This is domain-specific
- All React projects should follow patterns like:
  - Components in `src/components/`
  - Colocate styles with component
  - One component per directory

**Where to document**: `docs/domains/frontend/context.md`

**Why domain**: All frontend teams benefit from shared conventions

**Why not project**: Pattern applies to ANY React project (current or future)

---

### Example 3: Pagination API Response Format

**Question**: How should API paginated responses look?

**Answer**: This could be either:

**If all APIs should paginate the same way** (global standard):
- Document in: `docs/_global/context.md`
- Use: `{ data: [...], meta: { page, total } }`

**If only backend-api paginates this way** (project chose):
- Document in: `docs/projects/backend-api/context.md`
- Use: `{ data: [...], cursor, hasMore }`

**Typical approach**: Document in global (consistency), override in project if needed

---

### Example 4: Error Code Naming

**Question**: How should error codes be named?

**Answer**: Domain-specific (applies to all backend services)
- Format: `UPPERCASE_SNAKE_CASE`
- Examples: `USER_NOT_FOUND`, `INVALID_EMAIL`

**Where to document**: `docs/domains/backend/context.md`

**Why domain**: All backend APIs should follow same pattern

---

## Updating Context Files

### When to Update Project Context

- Your project chose a new technology
- Your project discovered a tricky pattern or gotcha
- Your project made an architectural decision
- Your team learned something project-specific

```shell
# After discovery
vi docs/projects/{your-project}/context.md    # Update tech stack or architecture
vi docs/projects/{your-project}/memory.md     # Document the learning
```

### When to Update Domain Context

- Pattern works across multiple projects
- Architectural approach benefits all team members of that role
- Convention should be standardized across the domain

```shell
# After discovery or agreement
vi docs/domains/{backend|frontend}/context.md   # Document pattern
vi docs/domains/{backend|frontend}/memory.md    # Document why/when
```

### When to Update Global Context

- Policy affects entire company
- Security or compliance requirement
- Team-wide agreement on process

```shell
# After major decision
vi docs/_global/context.md      # Update architecture or standards
vi docs/_global/memory.md       # Document decision and rationale
```

---

## Quick Reference

| Context Level | Scope | Update Frequency | Owner |
|---------------|-------|------------------|-------|
| Global | Company-wide | Quarterly | All team leads |
| Project | Single project | Monthly | Project tech lead |
| Domain | Backend/Frontend role | Monthly | Domain expert |
| Path | Specific directory | As needed | Domain expert |
| Feature | Specific feature | Per feature | Feature owner |

---

**Version**: 1.0  
**Last Updated**: February 23, 2026
