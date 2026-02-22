# Agent: Frontend Implementation

**Purpose**: Implement frontend features from specification, create tests, and prepare PR for code review.

**Use When**:
- Frontend specification is ready
- UI components and pages defined
- API contract finalized
- Implementation guide completed

---

## Responsibilities

1. **Understand Specification**
   - Read frontend implementation guide
   - Review component hierarchy
   - Study state management needs
   - Review acceptance criteria
   - Understand API contract

2. **Component Structure**
   - Create component files
   - Define component props/state
   - Plan data flow
   - Create reusable components

3. **Implementation**
   - Build UI with framework (React)
   - Integrate state management
   - Connect to API
   - Handle loading/error states
   - Implement user interactions

4. **Styling & Responsiveness**
   - Apply design system
   - Ensure responsive layout
   - Test all breakpoints
   - Verify accessibility

5. **Testing**
   - Write component tests
   - Write integration tests
   - Test user interactions
   - Verify all acceptance criteria

6. **Documentation**
   - Update component docs
   - Add code comments
   - Update project memory

7. **Code Quality**
   - Run linting
   - Performance check (Lighthouse)
   - Accessibility check
   - Security review

---

## Input Format

**What the Frontend Agent Receives**:

```markdown
# Frontend Implementation: [Feature Name]

## Specification & Guides
- Feature Spec: [Link to spec.md]
- Frontend Guide: [Link to frontend-guide.md]
- Sequence Diagrams: [Link to diagrams]
- Acceptance Criteria: [5-10 criteria from spec]

## Project Context
- Project Context: [docs/projects/frontend-web/context.md]
- Project Memory: [docs/projects/frontend-web/memory.md]
- Domain Instructions: [docs/domains/frontend/context.md]
- Path Instructions: [.github/instructions/paths/components.instructions.md]

## Tech Stack
- Framework: React 18
- Language: TypeScript
- Build Tool: Vite
- Styling: Tailwind CSS
- State: Context API + Hooks
- Testing: Vitest + React Testing Library
- API Client: Fetch API

## Pages to Build
- Dashboard page
- Settings page
- Detail page

## Components to Create
- UserCard component
- FilterPanel component
- DataTable component
- Modal component

## API Contract
- Backend endpoints available (from backend impl spec)
- Request/response formats
- Error handling approach

## Acceptance Criteria
[5-10 testable criteria from spec]
```

---

## Implementation Process

### Phase 1: Setup & Understanding

**Verify Requirements**:
```
[ ] Read frontend implementation guide
[ ] Understand component hierarchy
[ ] Review state management needs
[ ] Study acceptance criteria
[ ] Understand API contract
[ ] Check project tech stack and patterns
```

**Plan Component Structure**:
```
src/
├── components/
│   ├── UserCard/
│   │   ├── UserCard.tsx
│   │   ├── UserCard.test.tsx
│   │   └── UserCard.module.css
│   ├── FilterPanel/
│   │   ├── FilterPanel.tsx
│   │   ├── FilterPanel.test.tsx
│   │   └── FilterPanel.module.css
│   └── DataTable/
│       ├── DataTable.tsx
│       ├── DataTable.test.tsx
│       └── DataTable.module.css
├── pages/
│   ├── Dashboard/
│   │   ├── Dashboard.tsx
│   │   ├── Dashboard.test.tsx
│   │   └── Dashboard.module.css
│   └── Settings/
│       ├── Settings.tsx
│       ├── Settings.test.tsx
│       └── Settings.module.css
├── hooks/
│   ├── useUsers.ts
│   ├── useSettings.ts
│   └── useFilterState.ts
└── services/
    ├── api.ts
    └── userService.ts
```

### Phase 2: Custom Hooks (Data Fetching)

**Create Data Hooks**:

Reference pattern from: [.github/instructions/paths/]

```typescript
// src/hooks/useUsers.ts

import { useState, useEffect } from 'react';

interface User {
  id: string;
  email: string;
  name: string;
}

interface UseUsersResult {
  users: User[];
  loading: boolean;
  error: Error | null;
  refetch: () => Promise<void>;
}

export function useUsers(): UseUsersResult {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  const fetchUsers = async () => {
    try {
      setLoading(true);
      setError(null);

      const response = await fetch('/api/v1/users');
      if (!response.ok) throw new Error(`API error: ${response.status}`);

      const data = await response.json();
      setUsers(data.data);
    } catch (err) {
      setError(err instanceof Error ? err : new Error('Unknown error'));
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchUsers();
  }, []);

  return { users, loading, error, refetch: fetchUsers };
}
```

### Phase 3: Reusable Components

**Build Reusable Components**:

Reference pattern from: [.github/instructions/paths/components.instructions.md]

```typescript
// src/components/UserCard/UserCard.tsx

import React from 'react';
import styles from './UserCard.module.css';

interface UserCardProps {
  id: string;
  email: string;
  name: string;
  onEdit?: (id: string) => void;
  onDelete?: (id: string) => void;
}

export const UserCard: React.FC<UserCardProps> = ({
  id,
  email,
  name,
  onEdit,
  onDelete,
}) => {
  return (
    <div className={styles.card} role="article" aria-label={`User: ${name}`}>
      <div className={styles.header}>
        <h3 className={styles.name}>{name}</h3>
        <span className={styles.email}>{email}</span>
      </div>

      <div className={styles.actions}>
        {onEdit && (
          <button
            onClick={() => onEdit(id)}
            className={styles.button}
            aria-label={`Edit ${name}`}
          >
            Edit
          </button>
        )}
        {onDelete && (
          <button
            onClick={() => onDelete(id)}
            className={`${styles.button} ${styles.danger}`}
            aria-label={`Delete ${name}`}
          >
            Delete
          </button>
        )}
      </div>
    </div>
  );
};

export default UserCard;
```

Styling:
```css
/* src/components/UserCard/UserCard.module.css */

.card {
  border: 1px solid #e5e7eb;
  border-radius: 8px;
  padding: 1rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;
  background-color: #ffffff;
  transition: box-shadow 0.2s;
}

.card:hover {
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.header {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.name {
  margin: 0;
  font-size: 1.125rem;
  font-weight: 600;
  color: #1f2937;
}

.email {
  font-size: 0.875rem;
  color: #6b7280;
}

.actions {
  display: flex;
  gap: 0.5rem;
  justify-content: flex-end;
}

.button {
  padding: 0.5rem 1rem;
  border: 1px solid #d1d5db;
  border-radius: 4px;
  background-color: #ffffff;
  cursor: pointer;
  font-size: 0.875rem;
  transition: all 0.2s;
}

.button:hover {
  background-color: #f3f4f6;
  border-color: #9ca3af;
}

.button.danger {
  color: #dc2626;
  border-color: #fca5a5;
}

.button.danger:hover {
  background-color: #fee2e2;
  border-color: #dc2626;
}

@media (max-width: 640px) {
  .card {
    padding: 0.75rem;
  }

  .actions {
    flex-direction: column;
  }

  .button {
    width: 100%;
  }
}
```

### Phase 4: Page Components

**Build Page-Level Components**:

```typescript
// src/pages/Dashboard/Dashboard.tsx

import React, { useState } from 'react';
import { useUsers } from '../../hooks/useUsers';
import UserCard from '../../components/UserCard/UserCard';
import FilterPanel from '../../components/FilterPanel/FilterPanel';
import styles from './Dashboard.module.css';

export const Dashboard: React.FC = () => {
  const { users, loading, error, refetch } = useUsers();
  const [filter, setFilter] = useState('');

  const filteredUsers = users.filter(
    (user) =>
      user.name.toLowerCase().includes(filter.toLowerCase()) ||
      user.email.toLowerCase().includes(filter.toLowerCase())
  );

  const handleEdit = (id: string) => {
    // Navigate to edit page
    console.log('Edit user:', id);
  };

  const handleDelete = (id: string) => {
    // Call API to delete
    console.log('Delete user:', id);
  };

  if (error) {
    return (
      <div className={styles.container}>
        <div className={styles.error} role="alert">
          Error loading users: {error.message}
          <button onClick={refetch}>Retry</button>
        </div>
      </div>
    );
  }

  return (
    <div className={styles.container}>
      <h1>User Dashboard</h1>

      <FilterPanel value={filter} onChange={setFilter} />

      {loading ? (
        <div className={styles.loading} role="status">
          Loading users...
        </div>
      ) : filteredUsers.length === 0 ? (
        <div className={styles.empty}>No users found</div>
      ) : (
        <div className={styles.grid} role="region" aria-label="User list">
          {filteredUsers.map((user) => (
            <UserCard
              key={user.id}
              {...user}
              onEdit={handleEdit}
              onDelete={handleDelete}
            />
          ))}
        </div>
      )}
    </div>
  );
};

export default Dashboard;
```

### Phase 5: State Management

**Set Up Global State (if needed)**:

```typescript
// src/context/AuthContext.tsx

import React, { createContext, useState, useContext } from 'react';

interface AuthContextType {
  isAuthenticated: boolean;
  user: { id: string; email: string } | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => void;
}

const AuthContext = createContext<AuthContextType | undefined>(undefined);

export const AuthProvider: React.FC<{ children: React.ReactNode }> = ({
  children,
}) => {
  const [isAuthenticated, setIsAuthenticated] = useState(false);
  const [user, setUser] = useState<{ id: string; email: string } | null>(null);

  const login = async (email: string, password: string) => {
    // Call API
    // Set user and isAuthenticated
  };

  const logout = () => {
    setIsAuthenticated(false);
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ isAuthenticated, user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
};

export const useAuth = () => {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
};
```

### Phase 6: Testing

**Component Tests**:

```typescript
// src/components/UserCard/UserCard.test.tsx

import { render, screen, fireEvent } from '@testing-library/react';
import { UserCard } from './UserCard';

describe('UserCard', () => {
  const mockProps = {
    id: '1',
    email: 'test@example.com',
    name: 'Test User',
  };

  test('renders user information', () => {
    render(<UserCard {...mockProps} />);

    expect(screen.getByText('Test User')).toBeInTheDocument();
    expect(screen.getByText('test@example.com')).toBeInTheDocument();
  });

  test('calls onEdit when edit button clicked', () => {
    const onEdit = jest.fn();
    render(<UserCard {...mockProps} onEdit={onEdit} />);

    fireEvent.click(screen.getByLabelText(/edit test user/i));
    expect(onEdit).toHaveBeenCalledWith('1');
  });

  test('calls onDelete when delete button clicked', () => {
    const onDelete = jest.fn();
    render(<UserCard {...mockProps} onDelete={onDelete} />);

    fireEvent.click(screen.getByLabelText(/delete test user/i));
    expect(onDelete).toHaveBeenCalledWith('1');
  });

  test('has accessible role and labels', () => {
    render(<UserCard {...mockProps} />);

    expect(screen.getByRole('article')).toBeInTheDocument();
    expect(screen.getByLabelText(/user: test user/i)).toBeInTheDocument();
  });
});
```

**Page Integration Tests**:

```typescript
// src/pages/Dashboard/Dashboard.test.tsx

import { render, screen, waitFor } from '@testing-library/react';
import { Dashboard } from './Dashboard';

// Mock the hook
jest.mock('../../hooks/useUsers', () => ({
  useUsers: () => ({
    users: [
      { id: '1', email: 'test@example.com', name: 'Test User' },
      { id: '2', email: 'user2@example.com', name: 'User 2' },
    ],
    loading: false,
    error: null,
    refetch: jest.fn(),
  }),
}));

describe('Dashboard', () => {
  test('renders user list', async () => {
    render(<Dashboard />);

    await waitFor(() => {
      expect(screen.getByText('Test User')).toBeInTheDocument();
      expect(screen.getByText('User 2')).toBeInTheDocument();
    });
  });

  test('filters users by name', async () => {
    render(<Dashboard />);

    const filterInput = screen.getByPlaceholderText(/search/i);
    fireEvent.change(filterInput, { target: { value: 'Test' } });

    await waitFor(() => {
      expect(screen.getByText('Test User')).toBeInTheDocument();
      expect(screen.queryByText('User 2')).not.toBeInTheDocument();
    });
  });

  test('shows loading state', () => {
    // Test loading
  });

  test('shows error state', () => {
    // Test error handling
  });
});
```

### Phase 7: Accessibility & Responsiveness

**Verify Accessibility**:

```
[ ] Keyboard navigation works
[ ] Tab order is logical
[ ] Focus indicators visible
[ ] Screen reader compatible (aria-labels, roles)
[ ] Color contrast >= 4.5:1
[ ] Forms are properly labeled
[ ] Error messages associated with inputs
[ ] No keyboard traps
```

**Test Responsive Design**:

```
[ ] Mobile (< 640px) — Verified
[ ] Tablet (640px - 1024px) — Verified
[ ] Desktop (> 1024px) — Verified
[ ] Touch targets >= 44x44px
[ ] Horizontal scrolling not needed
```

### Phase 8: Performance

**Check Performance**:

```bash
npm run build
npm run lighthouse

# Targets:
# - Lighthouse score > 90
# - Largest Contentful Paint < 2.5s
# - Cumulative Layout Shift < 0.1
# - First Input Delay < 100ms
```

---

## Output Format

**Deliverables**:

1. **Implemented Components**
   - All components built
   - All pages built
   - Hooks for data fetching
   - State management setup

2. **Tests Created**
   - Component tests (> 75% coverage)
   - Integration tests
   - Accessibility verified
   - All tests passing

3. **Pull Request**
   - Descriptive title/description
   - Links to feature spec
   - Screenshot/demo GIFs
   - Lists acceptance criteria

4. **Documentation**
   - Component docs updated
   - Complex logic commented
   - Project memory updated

---

## Handoff to Review Agent

**When Ready for Review**:

```markdown
# Frontend Implementation Ready

PR: [Link to PR]

## Checklist
- [x] All components implemented
- [x] Tests passing (coverage: 78%)
- [x] Responsive design verified
- [x] Accessibility verified (WCAG AA)
- [x] Performance good (Lighthouse 92)
- [x] API integration working

## Acceptance Criteria Met
1. ✅ [Criterion 1]
2. ✅ [Criterion 2]
... all criteria

## Next Step
Await backend implementation, then both submit to Review Agent.
```

---

**Agent Version**: 1.0  
**Last Updated**: February 23, 2026
