# Frontend Development Instructions

This document guides frontend development work when using Copilot. It establishes patterns, standards, and conventions for frontend engineers on the team.

## Context References

Always reference these files when working on frontend tasks:

- **Context**: [docs/domains/frontend/context.md](../../docs/domains/frontend/context.md) — UI framework, component patterns, styling approach
- **Memory**: [docs/domains/frontend/memory.md](../../docs/domains/frontend/memory.md) — Known issues, browser quirks, accessibility notes
- **Global**: [docs/_global/context.md](../../docs/_global/context.md) — Project-wide architecture

## Code Standards

### Language & Framework
- Primary language: (to be defined in context.md)
- Framework: (to be defined in context.md)
- Module bundler: (to be defined in context.md)

### Component Structure
- One component per file
- Props should be typed (TypeScript/PropTypes)
- Use functional components with hooks (React standards)
- Keep components focused: max ~300 lines of code

### Naming Conventions
- Components: `PascalCase` (e.g., `UserProfile.jsx`)
- Files: Component name matches file name
- Hooks: Start with `use` (e.g., `useUserData`)
- Constants: `UPPER_SNAKE_CASE` (e.g., `MAX_RETRIES`)
- Utilities: `camelCase` (e.g., `formatDate.js`)

### Styling
- Reference: [docs/domains/frontend/context.md](../../docs/domains/frontend/context.md) for styling approach (CSS, Tailwind, CSS-in-JS, etc.)
- Use BEM or component-scoped naming
- Avoid inline styles; use stylesheet or component styles
- Ensure responsive design (mobile-first approach)

### State Management
- Use local component state for UI-only state
- Use global state for shared app data (Redux, Context API, Zustand, etc.)
- Document what goes where in [docs/domains/frontend/context.md](../../docs/domains/frontend/context.md)

### API Integration
- Place API calls in hooks or services
- Use consistent error handling
- Include loading and error states
- Add retry logic where appropriate

## Testing Requirements

### Unit Tests
- Test component rendering and props
- Mock child components and external dependencies
- Minimum 75% coverage for component logic
- Use snapshot tests sparingly (brittle)

### Integration Tests
- Test user workflows (click, input, navigation)
- Test API integration with mocked endpoints
- Verify error states and edge cases

### E2E Tests
- Critical user paths (login, core features)
- Use consistent selectors (data-testid over class names)

### Naming Convention
- `test_component_given_input_expects_output`
- Example: `test_LoginForm_given_invalid_email_expects_error_message`

## Accessibility (a11y)

**Non-negotiable requirements**:
- All interactive elements must be keyboard accessible
- Images require alt text
- Form inputs require labels
- Color should not be the only indicator
- Heading hierarchy makes sense (h1 → h2 → h3)
- Links are distinguishable from regular text

**Tools**: Use axe DevTools, WAVE, or Lighthouse audit

## Common Tasks

### Implementing a Component
1. Read the feature spec: `docs/features/{feature}/spec.md`
2. Check design consistency: [docs/domains/frontend/context.md](../../docs/domains/frontend/context.md)
3. Use prompt: **implement-from-spec**
4. Include component tests
5. Verify a11y with accessibility checklist
6. Update `docs/domains/frontend/memory.md` with any learnings

### Refactoring a Component
1. Use prompt: **refactor-slice**
2. Maintain 100% of existing behavior
3. Run full test suite before submitting
4. Compare before/after performance

### Performance Optimization
1. Profile using DevTools (React Profiler, Chrome Performance tab)
2. Document baseline metrics before changes
3. Common techniques: code splitting, lazy loading, memoization
4. Verify improvement in real-world conditions

### Browser Compatibility Issue
1. Check: [docs/domains/frontend/memory.md](../../docs/domains/frontend/memory.md) for known issues
2. Use Copilot to find polyfills or workarounds
3. Document solution in memory.md with browser/version info

## Development Workflow

### Before Coding
- [ ] Feature spec is clear and approved
- [ ] Have context files open/referenced
- [ ] Understand component patterns from memory
- [ ] Check design mockups/requirements

### During Coding
- [ ] Follow naming conventions
- [ ] Write tests alongside code
- [ ] Use ESLint / Prettier for consistency
- [ ] Build responsive first (mobile-first)
- [ ] Check accessibility as you go

### Before Submitting
- [ ] All tests pass
- [ ] No console errors or warnings
- [ ] Accessibility checklist passed
- [ ] Performance acceptable
- [ ] Updated memory.md with learnings
- [ ] Commits are clear and descriptive

## Anti-Patterns to Avoid

❌ **DON'T**:
- Hardcode data; use environment variables
- Ignore loading/error states
- Create deeply nested components
- Use `index.js` as component name
- Skip accessibility for "speed"
- Inline styles instead of stylesheets
- Put complex logic in render methods (hooks friendly)
- Forget null/undefined checks

✅ **DO**:
- Prop-driven components
- Handle all state variations
- Flatten component hierarchy
- Use descriptive file names
- Test accessibility automatically
- Use consistent styling approach
- Keep business logic in hooks
- Defensive coding (null checks, try/catch)

## Browser Support

Target browsers: (to be defined in context.md)

Reference: [docs/domains/frontend/memory.md](../../docs/domains/frontend/memory.md) for known browser quirks

## Useful Commands

(Define project-specific commands in docs/domains/frontend/context.md, e.g., "npm start", "npm test", etc.)

## Questions?

- **Component pattern**: Review similar components in codebase
- **Browser issue**: Check `docs/domains/frontend/memory.md`
- **Design consistency**: Reference `docs/domains/frontend/context.md`
- **Stuck on implementation**: Use Copilot with the **implement-from-spec** prompt

---

**Instructions Version**: 1.0  
**Last Updated**: February 23, 2026  
**Applies To**: Frontend engineers, UI/UX developers, full-stack developers
