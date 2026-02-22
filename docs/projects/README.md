# Projects Directory

This directory organizes context and documentation by project. Each project is self-contained with its own tech stack, architecture, and feature specs.

## Project Discovery

| Project | Purpose | Tech Stack | Team | Owner |
|---------|---------|-----------|------|-------|
| [backend-api](./backend-api/) | Core API server | Node.js, Express, PostgreSQL | Backend team | [Owner name] |
| [frontend-web](./frontend-web/) | Web application | React, Next.js, TypeScript | Frontend team | [Owner name] |
| (Add more as needed) | | | | |

## Project Structure

Each project has:

```
projects/{project-name}/
├── README.md              (project overview)
├── context.md             (architecture, tech stack, conventions)
├── memory.md              (decisions, gotchas, learnings)
├── features/
│   ├── feature-1/
│   │   ├── spec.md        (feature specification)
│   │   ├── context.md     (optional: feature-specific context)
│   │   └── memory.md      (optional: feature-specific learnings)
│   └── feature-2/
│       └── spec.md
└── README.md              (optional: project-specific README)
```

## When to Use Project Context

**Read project context when...**
- Working on a feature in that project
- New to a project and need to understand tech choices
- Making architectural decisions for that project
- Migrating to a new version of a key dependency

**Read global context when...**
- Understanding cross-project decisions
- Company-wide standards or policies
- Security/compliance requirements
- Shared infrastructure decisions

## Creating a New Project

1. Create new directory: `projects/{project-name}/`
2. Copy template: [project-template/](./project-template/)
3. Fill in `context.md` with project-specific tech
4. Create `memory.md` as team learns
5. Add feature subdirectories as needed
6. Update table above

## Context Hierarchy (With Projects)

```
Global Context (company policies, security, compliance)
└── architecture.md, decisions.md
    ├── Project Context (project-specific decisions)
    │   ├── backend-api/context.md, memory.md
    │   ├── frontend-web/context.md, memory.md
    │   │
    │   ├── Domain Context (role-specific within project)
    │   │   ├── .github/instructions/backend.instructions.md
    │   │   ├── .github/instructions/frontend.instructions.md
    │   │   │
    │   │   └── Path Context (file-location-specific)
    │   │       ├── src/handlers/, src/services/, src/components/
    │   │       └── (all path instructions in .github/instructions/paths/)
    │   │
    │   └── Feature Context (specific feature in project)
    │       └── features/{feature-name}/spec.md
    │
    └── (applies to ALL projects unless overridden by project context)
```

**Most specific context wins** — If project says one thing and global says another, use project's version.

---

**Directory Version**: 1.0  
**Last Updated**: February 23, 2026
