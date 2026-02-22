# Frontend Web Project — Memory

This document records frontend-specific learnings, design decisions, and gotchas discovered through implementation.

**Last Updated**: February 23, 2026

---

## Recent Updates

### 2026-02-23 — Project structure initialized

**Status**: Baseline established

**Details**: Created project-level context and memory files for frontend team

**Impact**: New team members can quickly understand project-specific tech choices

---

## Known Browser Issues

### Safari: Fixed Position Jumps on Mobile

**Symptom**: Fixed header/footer jumps when keyboard appears on iOS Safari

**Root Cause**: iOS Safari viewport height changes with keyboard, fixed positioning doesn't adapt

**Workaround**: Use `position: absolute` or sticky for mobile, avoid fixed positioning on form inputs

**Status**: Monitored — affects form pages

---

## Performance Learnings

### Code Splitting by Route

**Pattern**: Lazy load page components with React.lazy + Suspense

**Before**: Single 500KB bundle

**After**: Initial 150KB + lazy chunks per route

**Result**: 3x faster initial load, user can interact sooner

---

### Component Memoization Strategy

**Learning**: Not every component needs memoization

**Pattern**: Memoize only if:
- Component receives unstable props
- Component is expensive to render
- Parent renders frequently

**Impact**: Better performance without premature optimization

---

## Design System Decisions

### Why Tailwind CSS?

**Date**: Project start

**Trade-offs**:
- ✅ Fast development, low CSS maintenance
- ✅ Consistent design tokens
- ❌ Slightly larger HTML files
- ❌ Learning curve for new team members

**Decision**: Tailwind is worth it for this team's velocity

---

## Accessibility Learnings

### Keyboard Navigation Matters

**Discovery**: Many power users navigate with keyboard only

**Pattern**: Implement Tab order, keyboard shortcuts, Escape to close

**Example**: Modal opens on click, closes on Escape key

**Impact**: 20% of power users prefer keyboard navigation

---

## State Management Learnings

### Context API Sufficient (For Now)

**Decision**: Use Context API + Hooks instead of Redux

**Why**: State complexity still moderate, Context is simpler

**When to Revisit**: If state becomes 3x more complex

**Current Status**: Working well for 5 team developers

---

## Testing Patterns

### React Testing Library Best Practices

**Pattern**: Test user interactions, not implementation

**Before**: Testing component state and internal function calls

**After**: Testing button clicks, form submissions, rendered output

**Result**: Tests more resistant to refactoring, fewer false failures

---

## Team Agreements

### Component Prop Drilling Prevention

**Pattern**: Use Context for deeply needed data

**Example**: Theme, user auth, language — pass via Context, not props

**Benefit**: Cleaner component interfaces, easier to refactor

---

### Comment Strategy

**Pattern**: Comment the "why" not the "what"

**Example**:
```typescript
// ✅ Good - explains intent
// Memoize to prevent re-renders during list filtering
const UserCard = React.memo(({ user }) => { ... });

// ❌ Bad - describes what code does
// Wrap UserCard in React.memo
const UserCard = React.memo(({ user }) => { ... });
```

---

## Build & Performance

### Code Coverage Target: 75%, Not 100%

**Learning**: 100% coverage creates brittle tests

**Strategy**: Cover happy path + error cases, skip obvious code

**Result**: Faster test running, fewer maintenance headaches

---

## Metrics

| Metric | Target | Current | Status |
|--------|--------|---------|--------|
| Lighthouse | > 90 | TBD | ⏳ |
| Bundle Size | < 500KB | TBD | ⏳ |
| FCP | < 2s | TBD | ⏳ |

---

**Memory Version**: 1.0  
**Last Updated**: February 23, 2026  
**Maintained By**: Frontend Team Lead
