# Frontend Domain Memory

This document records frontend-specific learnings, browser gotchas, performance optimizations, and patterns discovered through implementation. Update this continuously after each sprint and significant feature.

**Last Updated**: February 23, 2026

---

## Recent Updates

(Add new learnings at top with date)

### [YYYY-MM-DD] — [Learning Title]

**Context**: [Where/when this was discovered]

**Issue / Pattern**: [What we learned]

**Action Taken**: [What we changed or implemented]

**Impact**: [Performance, UX, developer experience, etc.]

**Reference**: [Link to PR, commit, or feature spec]

---

## Known Browser Issues & Workarounds

### Browser: iOS Safari — Fixed Position Jumps on Keyboard Show

**Symptom**: When keyboard appears on mobile iOS, fixed positioned elements (header, footer) jump around or become inaccessible.

**Root Cause**: iOS Safari viewport height changes when keyboard appears, but fixed positioning doesn't account for it.

**Workaround**:
```typescript
// Use position: absolute instead of fixed for mobile
const useResponsivePosition = () => {
  const isMobile = window.innerWidth < 768;
  return isMobile ? 'absolute' : 'fixed';
};
```

**Better Solution**: Avoid fixed positioning on mobile forms. Use sticky positioning instead.

**Status**: Resolved for critical flows, monitoring

---

### Browser: Chrome — Memory Leak with Event Listeners

**Symptom**: Memory usage grows over time when component mounts/unmounts rapidly.

**Root Cause**: Event listeners not properly cleaned up in useEffect cleanup function.

**Workaround**:
```typescript
// ✅ Correct: Cleanup listeners
useEffect(() => {
  const handler = () => {...};
  window.addEventListener('resize', handler);
  return () => window.removeEventListener('resize', handler);
}, []);
```

**Status**: Resolved through code review checklist

---

### Browser: Safari — Grid Layout with gap Alignment Issues

**Symptom**: `gap` property doesn't align children in Safari 14 and earlier.

**Workaround**:
```typescript
// Use margin instead of gap for older Safari
const useGridGap = () => {
  const [useGap, setUseGap] = useState(true);
  useEffect(() => {
    const gap = CSS.supports('gap', '1rem');
    setUseGap(gap);
  }, []);
  return useGap;
};
```

**Status**: Most users on Safari 15+, no longer critical

---

## Performance Patterns & Optimizations

### Pattern: Lazy Loading Routes

**Pattern**: Code-split routes to reduce initial bundle size

```typescript
// ✅ Good: Lazy loaded routes
const Dashboard = React.lazy(() => import('./pages/Dashboard'));
const Settings = React.lazy(() => import('./pages/Settings'));

const AppRoutes = () => (
  <Routes>
    <Route
      path="/dashboard"
      element={
        <Suspense fallback={<LoadingSpinner />}>
          <Dashboard />
        </Suspense>
      }
    />
  </Routes>
);
```

**Impact**: Initial bundle reduced by 40%, faster first load

---

### Pattern: React Virtual Scrolling for Large Lists

**Pattern**: Only render visible items in long lists

**Example** (using react-window):
```typescript
import { FixedSizeList as List } from 'react-window';

const UserList = ({ users }: Props) => (
  <List
    height={600}
    itemCount={users.length}
    itemSize={50}
  >
    {({ index, style }) => (
      <div style={style}>
        {users[index].name}
      </div>
    )}
  </List>
);
```

**Impact**: Rendering 10,000 items now takes < 100ms (was 5 seconds)

**Caution**: Don't use with sticky headers or dynamic sizing

---

### Pattern: Image Optimization with WebP

**Pattern**: Serve WebP format with fallback

```typescript
<picture>
  <source srcSet="image.webp" type="image/webp" />
  <img src="image.jpg" alt="Description" />
</picture>
```

**Impact**: Image sizes reduced 25-35% with WebP

---

### Pattern: Component-Level Code Splitting

**Pattern**: Split large components into lazy-loaded chunks

```typescript
// ✅ Good: Heavy component lazy loaded
const Modal = React.lazy(() => import('./Modal'));

const Page = () => {
  const [showModal, setShowModal] = useState(false);
  return (
    <>
      <button onClick={() => setShowModal(true)}>Open</button>
      {showModal && (
        <Suspense fallback="Loading...">
          <Modal onClose={() => setShowModal(false)} />
        </Suspense>
      )}
    </>
  );
};
```

**Impact**: Delayed Modal load until user needs it

---

## Accessibility Learnings

### Pattern: Keyboard Navigation

**Learning**: Many users navigate with keyboard only. Must support:
- Tab between elements
- Enter to activate
- Escape to close
- Arrow keys for lists/menus

**Implementation**:
```typescript
const Modal = () => {
  const handleKeyDown = (e: KeyboardEvent) => {
    if (e.key === 'Escape') onClose();
  };

  useEffect(() => {
    window.addEventListener('keydown', handleKeyDown);
    return () => window.removeEventListener('keydown', handleKeyDown);
  }, []);

  return <dialog open tabIndex={-1} />;
};
```

**Status**: All major interactions now keyboard accessible

---

### Pattern: Screen Reader Testing

**Learning**: Screen readers read content in DOM order, not visual order.

**Common Issue**:
```typescript
// ❌ Bad: Visual order differs from DOM order
<div style={{ display: 'flex', flexDirection: 'row-reverse' }}>
  <div>Important first visually, but second in DOM</div>
  <div>Less important visually, but first in DOM</div>
</div>

// ✅ Good: Visual and DOM order match
<div style={{ display: 'flex', flexDirection: 'row-reverse' }}>
  <div>Less important visually</div>
  <div>Important first in both visual and DOM order</div>
</div>
```

**Status**: Code review checklist includes accessibility order verification

---

## State Management Patterns

### Pattern: Context for Simple Global State

**When to Use**: Auth state, theme, language

**Example**:
```typescript
const AuthContext = createContext<AuthContextType | null>(null);

export const AuthProvider = ({ children }: Props) => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  return (
    <AuthContext.Provider value={{ user, loading }}>
      {children}
    </AuthContext.Provider>
  );
};

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) throw new Error('useAuth must be inside AuthProvider');
  return context;
};
```

**Learning**: Context causes re-renders of all consumers. Split into multiple contexts or use Zustand if you have large state graphs.

---

### Pattern: Zustand for Complex Global State

**When to Use**: Complex state with many actions, derived state

**Example**:
```typescript
const useStore = create((set, get) => ({
  todos: [],
  filter: 'all',
  addTodo: (text) => set((state) => ({
    todos: [...state.todos, { id: Date.now(), text }],
  })),
  filteredTodos: () => {
    const { todos, filter } = get();
    return filter === 'all' ? todos : todos.filter(t => t.completed === (filter === 'done'));
  },
}));
```

**Learning**: Zustand only re-renders components that use changed selectors. More performant than Context for large states.

---

## Form Handling Patterns

### Pattern: Form Library (React Hook Form)

**When to Use**: Complex forms with validation

**Example**:
```typescript
import { useForm } from 'react-hook-form';
import * as z from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';

const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
});

const LoginForm = () => {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: zodResolver(schema),
  });

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} />
      {errors.email && <span>{errors.email.message}</span>}
    </form>
  );
};
```

**Learning**: React Hook Form keeps form state in uncontrolled components (better performance than fully controlled forms).

**When NOT to Use**: Simple forms (single input), use useState instead.

---

## Team Agreements

### Code Review Checklist for Frontend

- [ ] Tests added (unit + component)
- [ ] Accessibility passed (keyboard, screen reader, color contrast)
- [ ] Responsive design verified on mobile/tablet/desktop
- [ ] No console errors or warnings
- [ ] Performance acceptable (Lighthouse > 90)
- [ ] Styling consistent with design tokens
- [ ] Component props properly typed (TypeScript)
- [ ] Documentation / comments updated

---

## Performance Metrics Tracking

| Metric | Target | Current | Trend |
|--------|--------|---------|-------|
| FCP | < 1.8s | [TBD] | [TBD] |
| LCP | < 2.5s | [TBD] | [TBD] |
| INP | < 200ms | [TBD] | [TBD] |
| CLS | < 0.1 | [TBD] | [TBD] |
| Bundle Size | < 500KB | [TBD] | [TBD] |

---

## Future Optimizations

- [ ] Implement service worker for offline support
- [ ] Consider Web Vitals optimization program
- [ ] Evaluate advanced image formats (AVIF)
- [ ] Virtual scrolling for dashboard data tables

---

**Memory Version**: 1.0  
**Last Updated**: February 23, 2026  
**Maintained By**: [Frontend Team Lead]
