# Path Instructions: src/components/ (React Components)

This file provides specific guidance for building React components in the components directory.

## File Organization

**Directory Structure**:
```
src/components/
├── Button/
│   ├── Button.tsx          (component)
│   ├── Button.module.css   (styles, if CSS modules)
│   ├── Button.test.tsx     (tests)
│   └── index.ts            (export)
├── Form/
│   ├── Form.tsx
│   ├── Form.module.css
│   ├── Form.test.tsx
│   └── index.ts
├── index.ts                (re-exports all components)
```

**File Naming**: `PascalCase` matching component name

**Why One Directory Per Component?**
- Easier to manage related files
- Clear what belongs together
- Simple to add styles, tests without cluttering

## Component Structure

### Template

```typescript
import React from 'react';
import styles from './ComponentName.module.css';

export interface ComponentNameProps {
  /**
   * Description of this prop
   * @default undefined
   */
  prop1: string;

  /**
   * Optional prop with default
   * @default false
   */
  prop2?: boolean;

  /**
   * Callback when user interacts
   */
  onAction?: (data: unknown) => void;
}

/**
 * ComponentName
 *
 * [1-2 sentence description of what this component does]
 *
 * @example
 * ```tsx
 * <ComponentName prop1="value" onAction={() => console.log('clicked')} />
 * ```
 */
export const ComponentName: React.FC<ComponentNameProps> = ({
  prop1,
  prop2 = false,
  onAction,
}) => {
  const [state, setState] = React.useState(false);

  const handleClick = () => {
    setState(!state);
    onAction?.();
  };

  return (
    <div className={styles.container}>
      <button onClick={handleClick} className={styles.button}>
        {prop1}
      </button>
      {state && <div>{prop2 && <p>Optional content</p>}</div>}
    </div>
  );
};

export default ComponentName;
```

## Props & TypeScript

### Always Type Props

```typescript
// ✅ Good
interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  onClick: () => void;
  children: React.ReactNode;
}

// ❌ Bad - uses `any`
export const Button = (props: any) => { ... };
```

### Document Non-Obvious Props

```typescript
interface FormProps {
  /**
   * Form fields to render
   * @example
   * [{ name: 'email', type: 'email', required: true }]
   */
  fields: Field[];

  /**
   * Callback fired when form submitted
   * @param data - Validated form data
   */
  onSubmit: (data: Record<string, unknown>) => void;
}
```

### Prop Defaults

```typescript
interface MyComponentProps {
  // With default
  variant?: 'primary' | 'secondary'; // defaults to 'primary' in code
  
  // Without default (required)
  title: string;
  
  // Optional (no default needed)
  description?: string;
}

// Apply defaults in destructuring
const MyComponent: React.FC<MyComponentProps> = ({
  variant = 'primary',
  title,
  description,
}) => {
  // ...
};
```

## Styling

### CSS Modules

```typescript
// Button.tsx
import styles from './Button.module.css';

export const Button = () => (
  <button className={styles.button}>Click me</button>
);
```

```css
/* Button.module.css */
.button {
  padding: 8px 16px;
  background: var(--color-primary);
  border: none;
  cursor: pointer;
}

.button:hover {
  opacity: 0.9;
}

.button.disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
```

### Responsive Classes

```css
/* Mobile-first */
.container {
  display: grid;
  grid-template-columns: 1fr;
}

@media (min-width: 768px) {
  .container {
    grid-template-columns: 1fr 1fr;
  }
}
```

## State Management

### Local State (Component Only)

```typescript
// ✅ Good - form input is local
const Form = () => {
  const [email, setEmail] = React.useState('');
  const [password, setPassword] = React.useState('');
  // ...
};
```

### Global State (Shared)

```typescript
// ✅ Good - auth is global
const LogoutButton = () => {
  const { logout } = useAuth(); // from Context or Redux
  return <button onClick={logout}>Logout</button>;
};
```

### Lifting State

```typescript
// ✅ Good - lift state to parent
const Parent = () => {
  const [selectedUser, setSelectedUser] = React.useState(null);
  return (
    <>
      <UserSelect onSelect={setSelectedUser} />
      <UserDetails user={selectedUser} />
    </>
  );
};
```

## Hooks Usage

### Custom Hooks Pattern

```typescript
// hooks/useUserData.ts
export const useUserData = (userId: string) => {
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
export const UserProfile = ({ userId }: Props) => {
  const { user, loading } = useUserData(userId);
  if (loading) return <div>Loading...</div>;
  return <div>{user.name}</div>;
};
```

### Effect Cleanup

```typescript
// ✅ Good - cleanup listeners
React.useEffect(() => {
  const handler = () => handleResize();
  window.addEventListener('resize', handler);
  return () => window.removeEventListener('resize', handler);
}, []);
```

## Performance Optimization

### Memoization

```typescript
// ✅ Memoize if expensive to render or receives unstable props
export const UserCard = React.memo(({ user }: { user: User }) => {
  return <div>{user.name}</div>;
});
```

### useCallback

```typescript
// ✅ Memoize callback if passed to optimized child
const Parent = () => {
  const handleClick = React.useCallback(() => {
    console.log('clicked');
  }, []); // Empty deps = never changes

  return <Child onClick={handleClick} />;
};
```

### Lazy Loading

```typescript
// ✅ Code split heavy components
const HeavyComponent = React.lazy(() => import('./HeavyComponent'));

export const Page = () => (
  <React.Suspense fallback={<Loading />}>
    <HeavyComponent />
  </React.Suspense>
);
```

## Components Size Guideline

| Lines | Action |
|-------|--------|
| < 100 | Keep in single file |
| 100-300 | Consider breaking into sub-components |
| > 300 | Must break into smaller components |

## Testing

### Test Component Rendering & Props

```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

describe('Button', () => {
  it('renders with children', () => {
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

### Test User Interactions

```typescript
// ✅ Test what users can do, not implementation
it('submits form when filled', async () => {
  const onSubmit = jest.fn();
  const user = userEvent.setup();
  
  render(<LoginForm onSubmit={onSubmit} />);
  
  await user.type(screen.getByLabelText('Email'), 'test@example.com');
  await user.type(screen.getByLabelText('Password'), 'password123');
  await user.click(screen.getByRole('button', { name: 'Login' }));
  
  expect(onSubmit).toHaveBeenCalledWith({
    email: 'test@example.com',
    password: 'password123',
  });
});
```

## Documentation

### Component Comments

```typescript
/**
 * Button component for user interactions
 *
 * Supports multiple variants and sizes with consistent styling.
 * Can be disabled and shows loading state during async operations.
 *
 * @example
 * ```tsx
 * <Button variant="primary" onClick={handleClick}>
 *   Submit
 * </Button>
 * ```
 */
export const Button: React.FC<ButtonProps> = ({ ... }) => { ... };
```

### README in Component Folder

For complex components, add `README.md`:

```markdown
# UserCard Component

... detailed docs ...
```

## Export Pattern

```typescript
// src/components/index.ts
export { Button } from './Button';
export { UserCard } from './UserCard';
export { Form } from './Form';
// etc.
```

Usage:
```typescript
import { Button, UserCard } from '../components';
```

## Anti-Patterns to Avoid

❌ **DON'T**:
- Pass entire objects as props if only one field is needed
- Create components > 300 lines
- Hardcode API calls in components
- Skip prop typing
- Use index as key in lists (causes re-render bugs)
- Leave components without tests
- Put business logic in components

✅ **DO**:
- Keep components small and focused
- Let parent manage state, component receives via props
- Use custom hooks for stateful logic
- Type all props
- Use unique, stable keys for list items
- Test component behavior
- Keep components presentational where possible

---

**Instructions Version**: 1.0  
**Last Updated**: February 23, 2026
