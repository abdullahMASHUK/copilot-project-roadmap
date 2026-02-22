# Frontend Domain Context

This document describes frontend architecture, UI framework, component patterns, and conventions. Reference this when building frontend features and components.

**Last Updated**: February 23, 2026

---

## Framework & Technology Stack

**Framework**: [React / Vue / Angular / Other]

**Version**: [Version number]

**Build Tool**: [Vite / Webpack / Next.js / Other]

**Package Manager**: npm / yarn / pnpm

**Language**: TypeScript / JavaScript

**Styling**: [CSS-in-JS / Tailwind / BEM / SCSS / Other]

---

## UI Architecture

### Component Hierarchy

[Describe overall component structure]

### Key Components

| Component | Path | Purpose | Owner |
|-----------|------|---------|-------|
| [Component] | `src/components/` | [Purpose] | [Owner] |

### Application Structure

```
src/
├── components/     # Reusable components
│   ├── Button.tsx
│   ├── Card.tsx
│   └── ...
├── pages/          # Page components (if using routing)
├── services/       # API service functions
├── hooks/          # Custom React hooks
├── state/          # State management (Redux, Zustand, etc.)
├── utils/          # Utility functions
├── styles/         # Global styles
└── types/          # TypeScript type definitions
```

---

## Component Patterns

### Functional Component Pattern

**Standard Structure**:

```typescript
interface ComponentProps {
  prop1: string;
  prop2?: number;
  onAction?: () => void;
}

export const Component: React.FC<ComponentProps> = ({
  prop1,
  prop2 = 0,
  onAction,
}) => {
  const [state, setState] = React.useState(false);

  return (
    <div>
      {/* JSX */}
    </div>
  );
};

export default Component;
```

**Rules**:
- Use TypeScript for prop types
- Export named component AND default export
- Destructure props in function signature
- Keep components focused (< 300 lines)

### Custom Hooks Pattern

**Pattern**: Extract stateful logic into custom hooks

```typescript
// ✅ Good: Logic in custom hook
const useUserData = (userId: string) => {
  const [user, setUser] = React.useState(null);
  const [loading, setLoading] = React.useState(false);

  React.useEffect(() => {
    setLoading(true);
    fetchUser(userId)
      .then(setUser)
      .finally(() => setLoading(false));
  }, [userId]);

  return { user, loading };
};

// Usage in component
const UserProfile = ({ userId }: Props) => {
  const { user, loading } = useUserData(userId);
  // ...
};
```

**Benefits**: Reusable logic, easier testing, cleaner components

---

## Styling

### Approach

**Method**: [CSS Modules / Tailwind / Styled Components / SCSS / Other]

**Naming**: Component name matches file name

**Example**:
```typescript
// UserCard.tsx
import styles from './UserCard.module.css';

export const UserCard = () => (
  <div className={styles.container}>
    <h2 className={styles.title}>User Name</h2>
  </div>
);
```

### Design Tokens

**Colors**:
- Primary: [Color code]
- Secondary: [Color code]
- Error: [Color code]

**Typography**:
- Heading: [Font, size, weight]
- Body: [Font, size, weight]

**Spacing**: [8px scale or custom]

**Breakpoints**:
- Mobile: < 768px
- Tablet: 768px - 1024px
- Desktop: > 1024px

---

## State Management

### Approach

**Tool**: [Redux / Context API / Zustand / Other]

**What Goes Where**:

**Global State** (shared across multiple pages):
- User authentication
- User profile
- App settings

**Local State** (component only):
- Form input
- UI toggles
- Temporary data

### Example Pattern

```typescript
// ✅ Good: Local state for form
const LoginForm = () => {
  const [email, setEmail] = React.useState('');
  const [password, setPassword] = React.useState('');
  // ...
};

// ✅ Good: Global state for auth
const UserHeader = () => {
  const { user, isLoggedIn } = useAuth(); // from Context/Redux
  // ...
};
```

---

## API Integration

### Service Pattern

**Location**: `src/services/api.ts` (or separate files per domain)

**Pattern**:
```typescript
export const userService = {
  get: (id: string) => fetch(`/api/v1/users/${id}`),
  create: (data) => fetch('/api/v1/users', { method: 'POST', body: JSON.stringify(data) }),
  update: (id: string, data) => fetch(`/api/v1/users/${id}`, { method: 'PUT' }),
};
```

**Error Handling**:
```typescript
export const userService = {
  get: async (id: string) => {
    try {
      const response = await fetch(`/api/v1/users/${id}`);
      if (!response.ok) throw new Error('Failed to fetch user');
      return response.json();
    } catch (error) {
      console.error('User fetch error:', error);
      throw error;
    }
  },
};
```

### Hook for Data Fetching

```typescript
const useUser = (userId: string) => {
  const [data, setData] = React.useState(null);
  const [loading, setLoading] = React.useState(false);
  const [error, setError] = React.useState<Error | null>(null);

  React.useEffect(() => {
    setLoading(true);
    userService.get(userId)
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [userId]);

  return { data, loading, error };
};
```

---

## Testing

### Unit Tests

**Location**: `src/components/__tests__/`

**Pattern**:
```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Button } from '../Button';

describe('Button', () => {
  it('renders button with text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button')).toHaveTextContent('Click me');
  });

  it('calls onClick when clicked', async () => {
    const onClick = jest.fn();
    const user = userEvent.setup();
    render(<Button onClick={onClick}>Click</Button>);
    await user.click(screen.getByRole('button'));
    expect(onClick).toHaveBeenCalled();
  });
});
```

**Coverage Target**: 75% for component logic

### Testing Best Practices

- Test user interactions, not implementation
- Use `userEvent` over `fireEvent`
- Use `data-testid` for hard-to-select elements
- Mock external dependencies
- Never test styling (use E2E for that)

---

## Accessibility (WCAG 2.1 Level AA)

### Requirements (Non-Negotiable)

- [ ] All interactive elements keyboard accessible
- [ ] Form inputs have labels
- [ ] Images have alt text
- [ ] Color is not sole indicator of meaning
- [ ] Focus visible on all interactive elements
- [ ] Heading hierarchy correct (h1 → h2 → h3)
- [ ] Links distinct from regular text
- [ ] Color contrast ≥ 4.5:1

### Implementation

```typescript
// ✅ Good button
<button
  onClick={handleClick}
  aria-label="Close dialog"
  className={styles.button}
>
  ✕
</button>

// ✅ Good form
<label htmlFor="email">Email Address</label>
<input id="email" type="email" required />

// ✅ Good image
<img src="chart.png" alt="Sales by region chart" />
```

### Tools

- **Axe DevTools**: Browser extension for accessibility
- **WAVE**: Web accessibility checker
- **Lighthouse**: Audit in Chrome DevTools

---

## Performance

### Optimization Techniques

1. **Code Splitting**: Lazy load routes/components

```typescript
const Dashboard = React.lazy(() => import('./Dashboard'));

<Suspense fallback={<Loading />}>
  <Dashboard />
</Suspense>
```

2. **Memoization**: Prevent unnecessary re-renders

```typescript
export const UserCard = React.memo(({ user }: Props) => {
  return <div>{user.name}</div>;
});
```

3. **Image Optimization**: Use responsive images

```typescript
<img
  src="image.webp"
  srcSet="image-small.webp 480w, image.webp 1024w"
  sizes="(max-width: 480px) 480px, 1024px"
  alt="Description"
/>
```

### Performance Targets

| Metric | Target |
|--------|--------|
| First Contentful Paint (FCP) | < 1.8s |
| Largest Contentful Paint (LCP) | < 2.5s |
| Interaction to Next Paint (INP) | < 200ms |
| Cumulative Layout Shift (CLS) | < 0.1 |
| Lighthouse Score | > 90 |

---

## Responsive Design

### Mobile-First Approach

Build for mobile first, enhance for larger screens:

```typescript
// Tailwind example
<div className="
  grid grid-cols-1      // Mobile: 1 column
  md:grid-cols-2        // Tablet: 2 columns
  lg:grid-cols-3        // Desktop: 3 columns
">
```

### Breakpoints

- **Mobile**: < 768px
- **Tablet**: 768px - 1024px
- **Desktop**: > 1024px
- **Large**: > 1440px

## Browser Support

**Target Browsers**:
- Chrome (latest 2 versions)
- Firefox (latest 2 versions)
- Safari (latest 2 versions)
- Edge (latest 2 versions)

---

## Development Workflow

### Before Coding

- [ ] Design mockup approved
- [ ] API contract finalized
- [ ] Component structure planned

### During Coding

- [ ] TypeScript strict mode
- [ ] ESLint passing
- [ ] Tests written
- [ ] No console errors

### Before Submitting

- [ ] All tests pass
- [ ] Accessibility checklist passed
- [ ] Responsive design verified
- [ ] Performance acceptable (Lighthouse)
- [ ] No console warnings

---

## Useful Commands

```bash
# Development
npm start              # Start dev server
npm run build          # Production build

# Testing
npm test               # Run tests
npm run test:coverage  # Coverage report

# Linting & Formatting
npm run lint           # ESLint
npm run format         # Prettier

# Performance
npm run build:analyze  # Bundle size analysis
npm run lighthouse     # Run Lighthouse audit
```

---

## Questions?

- **Component pattern**: Check similar components in codebase
- **Styling approach**: Reference design tokens in [docs/domains/frontend/context.md](./context.md)
- **API integration**: Check existing services in `src/services/`
- **Accessibility question**: Check implementation examples above

---

**Domain Context Version**: 1.0  
**Last Updated**: February 23, 2026  
**Maintained By**: [Frontend Team Lead]
