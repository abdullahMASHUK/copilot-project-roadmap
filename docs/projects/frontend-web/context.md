# Frontend Web Project — Context

This document covers the frontend web application architecture, tech stack, and conventions specific to this project.

**Last Updated**: February 23, 2026

---

## Project Overview

**Purpose**: React web application for users to access platform features, manage data, and collaborate

**Users**: Desktop and tablet users (responsive design)

**Criticality**: Core — Main user interaction point

**Deployment**: SPA deployed to CDN, auto-updates on page refresh

---

## Technology Stack

| Layer | Choice | Version | Notes |
|-------|--------|---------|-------|
| **Framework** | React | 18.x | Latest stable |
| **Language** | TypeScript | 5.0 | Strict mode enabled |
| **Build Tool** | Vite | 4.x | Fast dev server, fast builds |
| **Routing** | React Router | 6.x | Client-side routing |
| **State** | Context API + Hooks | — | Sufficient for current needs |
| **Styling** | Tailwind CSS | 3.x | Utility-first framework |
| **Testing** | Vitest + React Testing Library | — | Unit & component tests |
| **API Client** | Fetch API | — | Custom hooks wrapper |
| **Deployment** | Vercel | — | Git-based deployment |

---

## Architecture

### High-Level Design

```
User Browser
    ↓
React Components (SPA)
    ↓
Custom Hooks (data fetching, state)
    ↓
API Service Layer
    ↓
Backend API
```

### Component Structure

**Layout Components**: Page structure

**Feature Components**: Reusable across features

**Page Components**: Route-specific pages

**Pattern**: One directory per component with styles and tests colocated

---

## UI Patterns

### Component Organization

```
src/
├── components/      (reusable UI components)
├── pages/          (page-level components per route)
├── hooks/          (custom React hooks)
├── services/       (API communication)
├── state/          (global state)
├── styles/         (global styles, design tokens)
└── utils/          (utility functions)
```

### Design System

**Colors**: [Tailwind defaults + brand colors]

**Typography**: [Font families and sizes]

**Spacing**: 8px base unit

**Breakpoints**:
- Mobile: < 768px
- Tablet: 768px - 1024px
- Desktop: > 1024px

---

## State Management

**Approach**: Context API + Custom Hooks

**Global State** (in Context):
- User authentication
- User profile
- App theme/settings

**Local State** (in Components):
- Form inputs
- UI toggles
- Temporary data

**API State** (in Custom Hooks):
- Loading, error, data from API calls

---

## Performance

**Optimization Strategy**:
- Code splitting by route
- Image optimization with WebP
- Component memoization where needed
- Lazy loading of heavy components

**Targets**:
- First Contentful Paint: < 2s
- Lighthouse: > 90
- Bundle size: < 500KB gzipped

---

## Testing

**Unit & Component Tests**:
- Framework: Vitest + React Testing Library
- Coverage: 75%+ for component logic
- Coverage: 80%+ for utility functions

**E2E Tests**:
- Tool: Cypress (if applicable)
- Focus: Critical user workflows

---

## Accessibility

**Standard**: WCAG 2.1 Level AA (required)

**Requirements**:
- Keyboard navigation
- Screen reader support
- Color contrast 4.5:1
- Focus indicators visible

---

## Deployment

**Platform**: Vercel (git-connected)

**Environments**:
- **Development**: Local dev server
- **Preview**: PR previews on Vercel
- **Production**: [app.example.com]

**Process**:
1. Push to main
2. Vercel auto-deploys to preview
3. Review in preview
4. Merge to production branch
5. Auto-deploys to production (CDN)

---

## API Integration

**Base URL**: Environment-based
```typescript
const apiUrl = process.env.REACT_APP_API_URL || 'https://api.example.com';
```

**Authentication**: Bearer token in localStorage (secure httpOnly would be better)

**Error Handling**: Custom error pages, retry logic

---

## Browser Support

**Target Browsers**:
- Chrome (latest 2 versions)
- Firefox (latest 2 versions)
- Safari (latest 2 versions)
- Edge (latest 2 versions)

---

## Key Decisions

See [memory.md](./memory.md) for specific decisions and trade-offs made for this project.

---

## Useful Commands

```bash
npm run dev        # Start dev server
npm test           # Run tests
npm run build      # Build for production
npm run lint       # Check code style
npm run type-check # TypeScript check
```

---

**Context Version**: 1.0  
**Last Updated**: February 23, 2026  
**Maintained By**: Frontend Team Lead
