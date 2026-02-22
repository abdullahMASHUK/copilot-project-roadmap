# Path-Specific Instructions Index

This file maps filesystem paths to specialized instructions. When working in a specific directory, reference both your domain instructions AND the applicable path instructions.

## Quick Lookup

| Path Pattern | Instructions | When to Use |
|--------------|--------------|-----------|
| `src/handlers/` or `src/routes/` | [api.instructions.md](./api.instructions.md) | Building API endpoints |
| `src/components/` | [components.instructions.md](./components.instructions.md) | Creating or modifying UI components |
| `src/services/` | [services.instructions.md](./services.instructions.md) | Building business logic services |
| `src/hooks/` | [hooks.instructions.md](./hooks.instructions.md) | Creating custom React hooks |
| `tests/` or `**/__tests__/` | [testing.instructions.md](./testing.instructions.md) | Writing any tests (unit, integration, E2E) |
| `migrations/` or `db/migrations/` | [database.instructions.md](./database.instructions.md) | Writing database migrations |
| `src/utils/` or `src/lib/` | [utilities.instructions.md](./utilities.instructions.md) | Building reusable utility functions |
| `.github/workflows/` | [workflows.instructions.md](./workflows.instructions.md) | Creating CI/CD workflows |
| `src/config/` or `config/` | [configuration.instructions.md](./configuration.instructions.md) | Managing project configuration |

## How to Use

When you're working in a specific directory:

1. **Identify your domain** — Are you working in backend? frontend? DevOps?
   - Read: [.github/instructions/backend.instructions.md](../backend.instructions.md) or equivalent

2. **Identify your path** — Is your file in `src/components/`? `tests/`? `migrations/`?
   - Read: Applicable path-specific file from table above

3. **Combine them** — Path-specific instructions supplement domain instructions
   - Path-specific = More detailed, more specific to that directory
   - Domain = General standards across your domain

**Example**: Building a React form component
- Read: [frontend.instructions.md](../frontend.instructions.md) — Component patterns, styling, state management
- Read: [components.instructions.md](./components.instructions.md) — Component file structure, prop naming, documentation
- Read: [hooks.instructions.md](./hooks.instructions.md) — If using custom hooks in the form

## When Path Instructions Override Domain Instructions

If there's a conflict between domain and path instructions, **path instructions are more specific and win**.

Example:
- Domain says: "Use any CSS approach"
- Path (forms/) says: "Use controlled components with react-hook-form"
- **Use react-hook-form** for forms

## Adding New Path Instructions

Before creating a new set of path instructions, ask:

**Is this path unique enough to warrant special handling?**

Good candidates for path instructions:
- Significant architectural patterns unique to this directory
- Complex setup/configuration specific to this path
- Team decisions that apply only to this path
- Error-prone patterns we want to prevent

Not good candidates:
- Just repeating domain standards
- Single file or very small scope
- Rare operations

**To add**: 
1. Create new `.instructions.md` file in this directory
2. Add entry to the table above
3. Link in root [copilot-instructions.md](../../copilot-instructions.md)
4. Document in [docs/changelog.md](../../../docs/changelog.md)

---

**Index Version**: 1.0  
**Last Updated**: February 23, 2026
