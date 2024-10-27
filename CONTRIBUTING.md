# Contributing Guide

This document outlines our strict coding standards aimed at maintaining exceptional code quality and readability in the Project.

## Core Principles

- **Readability Over Cleverness**: Code should be immediately understandable by other developers
- **Strict File Length**: Maximum 300 lines per file, aim for under 100 lines
- **Functional Programming**: Prefer pure functions and immutable data structures
- **Type Safety**: Strict TypeScript usage with no compromises

## Code Style Guidelines

### Functional Programming

- Write pure functions whenever possible
- Avoid state mutation and the `let` keyword
- Use immutable array methods exclusively:
  ```typescript
  // Don't
  for (let i = 0; i < items.length; i++) {
    items[i] = transform(items[i])
  }

  // Do
  const newItems = items.map(transform)
  ```

- Early returns are mandatory for improved readability:
  ```typescript
  // Don't
  function processUser(user: User) {
    if (user.isActive) {
      if (user.hasPermission) {
        return doSomething()
      }
    }
    return null
  }

  // Do
  function processUser(user: User) {
    if (!user.isActive) return null
    if (!user.hasPermission) return null
    return doSomething()
  }
  ```

### React Components

- **Single Responsibility**: Each component should do exactly one thing
- **Size Limit**: Components should rarely exceed 100 lines
- **Props Limit**: Maximum 5 props per component, use composition for more complex needs
- **Nesting Limit**: Maximum 5 levels of JSX nesting

#### Component Separation Guidelines

The separation between Display and Container components is **mandatory** in the following cases:
- When the same UI elements need to be reused across multiple features
- When the component handles complex data fetching or state management that could be reused
- When the component exceeds 100 lines due to mixing business logic with presentation
- When the same UI needs to support multiple data sources

In other cases, combining display and logic in a single component is acceptable, but consider separation when:
- The component's logic becomes complex enough to warrant testing in isolation
- You anticipate the UI being reused in the future
- The component handles multiple responsibilities that could be logically split

```typescript
// Example of when separation is required (reusable UI)
function UserCard({ name, email, onEdit }: UserCardProps): JSX.Element {
  return (
    <div className="p-4 border rounded">
      <h3 className="text-lg">{name}</h3>
      <p className="text-gray-600">{email}</p>
      <button onClick={onEdit}>Edit</button>
    </div>
  )
}

// Multiple containers can reuse the same display component
function AdminUserContainer(): JSX.Element {
  const { data: user } = useQuery(['admin-user'], fetchAdminUser)
  // ... admin-specific logic
  return <UserCard {...user} onEdit={handleAdminEdit} />
}

function CustomerUserContainer(): JSX.Element {
  const { data: user } = useQuery(['customer'], fetchCustomer)
  // ... customer-specific logic
  return <UserCard {...user} onEdit={handleCustomerEdit} />
}

// Example of when combining is acceptable (simple, single-use component)
function SimpleGreeting(): JSX.Element {
  const { data: user } = useQuery(['user'], fetchUser)
  
  if (!user) return <LoadingSpinner />
  
  return (
    <div className="p-2">
      <h2>Welcome, {user.name}!</h2>
    </div>
  )
}
```

#### Display Components (Presentational)
- Must be pure functions that only depend on their props
- No hooks (except component-specific style hooks)
- No API calls or data fetching
- No connection to global state
- Should be highly reusable
- Focus only on how things look
- Receive data and callbacks exclusively via props
- Can contain other Display components

```typescript
// Good Display Component
function UserCard({ name, email, onEdit }: UserCardProps): JSX.Element {
  return (
    <div className="p-4 border rounded">
      <h3 className="text-lg">{name}</h3>
      <p className="text-gray-600">{email}</p>
      <button onClick={onEdit}>Edit</button>
    </div>
  )
}
```

#### Container Components (Smart)
- Handle data fetching and state management
- Connect to global state management
- Contain business logic and data transformations
- Pass data down to Display components
- Should have minimal JSX
- Focus on how things work
- Must be colocated with their feature

```typescript
// Good Container Component
function UserCardContainer(): JSX.Element {
  const { data: user } = useQuery(['user'], fetchUser)
  const { mutate: updateUser } = useMutation(saveUser)

  const handleEdit = async (updates: UserUpdates): Promise<void> => {
    await updateUser(updates)
  }

  if (!user) return <LoadingSpinner />

  return <UserCard 
    name={user.name}
    email={user.email}
    onEdit={handleEdit}
  />
}
```

### TypeScript Usage

- `any` is forbidden except in test files
- `unknown` must be used instead of `any` for uncertain types
- Non-null assertions (`!`) are forbidden
- All functions must have explicit return types
- Generic types must have meaningful names (not T, U, V)

```typescript
// Don't
function process(data: any) {
  return data.value!
}

// Do
function process<InputType extends Record<string, unknown>>(data: InputType): string {
  if (!data.value || typeof data.value !== 'string') {
    throw new Error('Invalid input')
  }
  return data.value
}
```

### Styling

- Only Tailwind CSS classes are allowed
- No inline styles
- No CSS-in-JS
- Custom CSS only allowed in global.css for exceptional cases
- Color values must come from Tailwind theme configuration

```tsx
// Don't
<div style={{ marginLeft: '8px' }} className="text-blue-500">

// Do
<div className="ml-2 text-primary-500">
```

### File Organization

- Strict file naming:
  - React components: `PascalCase.tsx`
  - Utilities: `kebab-case.ts`
  - Tests: `ComponentName.test.tsx`
  - Types: `ComponentName.types.ts`

- Special directories for shadcn:
  - `components/ui/*`: Reserved ONLY for shadcn components
  - `lib/utils.ts`: Reserved ONLY for shadcn utilities
  
- Folder structure must follow:
```
components/                    # Reusable UI components
  ui/                        # shadcn components ONLY
    Button.tsx
    Dialog.tsx
  ExampleComponent/           # Each component in its own directory
    index.tsx                # Main component implementation
    ExampleComponent.types.ts   # Component-specific types and interfaces
    ExampleComponent.test.tsx   # Component tests
    ExampleComponent.utils.ts   # Component-specific utilities (used only by this component)
    ExampleComponent.hooks.ts   # Component-specific hooks (used only by this component)
    components/              # Child components used ONLY by this component
      ExampleChildComponent.tsx

lib/
  utils.ts                   # shadcn utilities ONLY

hooks/                       # Common hooks used across multiple components (could also include shadcn hooks, add those to the .eslintignore file)
  useExampleHook.ts
  useAnotherHook.ts
  
utils/                      # Common utility functions used across multiple components
  example-utils.ts
  format-utils.ts
  
types/                      # Common TypeScript types and interfaces
  example.types.ts
  models.types.ts
  
constants/                  # Global constants and configuration
  config.ts
  routes.ts
```

#### Component Organization Rules

- Component folders should contain ONLY code that is exclusively used by that component
- If a utility or hook is used by multiple components, it MUST be moved to the root-level `utils/` or `hooks/` directory
- Keep component folders focused - if they become too complex, split them into multiple components
- Child components that are only used by a single parent should live in that parent's `components/` directory
- Common types, constants, and utilities used across multiple components belong in their respective root-level directories
- shadcn components must remain isolated:
  - No modifications to `components/ui/*` files
  - No modifications to `lib/utils.ts`
  - Use composition instead of modifying shadcn components
- All project utilities must go in `/utils`, never in `lib/utils.ts`
- shadcn-related files are excluded from project linting:
  - `components/ui/*`
  - `lib/utils.ts`
  - Third-party hooks (e.g., `use-mobile`)

### Performance Guidelines

- No premature optimization
- Performance improvements must be backed by profiler data
- Required optimizations:
  - Use `React.memo` for expensive renders only
  - Implement proper key props in lists
  - Lazy load all routes
  - Keep bundle sizes under 100KB per chunk

### Code Review Requirements

- All PRs must:
  - Pass all TypeScript checks with strict mode
  - Have no ESLint warnings
  - Include tests for new functionality
  - Be under 300 lines total
  - Include updated documentation

## Development Workflow

1. Create feature branch from main
2. Write tests first (TDD approach)
3. Implement feature
4. Ensure all style guidelines are met
5. Create PR with detailed description
6. Address review comments
7. Squash and merge

## Enforcement

These guidelines are strictly enforced through:
- ESLint rules
- TypeScript compiler options
- Code Reviews

### State Management

- Use React Query for server state
- Use Zustand for client-only global state
- Prefer local state when possible
- Document all global state stores
- Implement proper loading and error states

```typescript
// Good state management
function UserProfile(): JSX.Element {
  // Server state
  const { data: user, isLoading, error } = useQuery(['user'], fetchUser)
  // Local state
  const [isEditing, setIsEditing] = useState(false)
  // Global state (only when necessary)
  const theme = useStore(state => state.theme)

  if (isLoading) return <LoadingSpinner />
  if (error) return <ErrorDisplay error={error} />
  if (!user) return <NotFound />

  return <UserDisplay user={user} theme={theme} />
}
```

### Documentation Standards

- All public functions must have JSDoc comments
- Include code examples in documentation
- Document all props with detailed descriptions
- Maintain a changelog
- Include setup instructions for new dependencies

```typescript
/**
 * Transforms user data for API submission
 * @param user - The user object to transform
 * @param options - Configuration options for the transformation
 * @returns Transformed user data ready for API submission
 * @throws {ValidationError} When user data is invalid
 * @example
 * const transformed = transformUserData({
 *   name: 'John',
 *   age: 30
 * }, { includeMetadata: true })
 */
function transformUserData(user: User, options: TransformOptions): ApiUser {
  // Implementation
}
```

### Testing Requirements

- Unit tests for all utility functions
- Integration tests for all API calls
- Component tests must include:
  - Happy path rendering
  - Error states
  - Loading states
  - User interactions
  - Edge cases
- E2E tests for critical user flows
- Maintain 80% code coverage minimum

```typescript
// Good test example
describe('UserCard', () => {
  it('renders user information correctly', () => {})
  it('handles missing optional fields', () => {})
  it('shows loading state', () => {})
  it('displays error message on API failure', () => {})
  it('handles edit user interaction', () => {})
})
```

### Accessibility Standards

- All images must have alt text
- Proper ARIA labels for interactive elements
- Keyboard navigation support
- Color contrast compliance
- Screen reader friendly markup
- Regular accessibility audits

```typescript
// Good accessibility practices
function Button({ onClick, children }: ButtonProps): JSX.Element {
  return (
    <button
      onClick={onClick}
      className="px-4 py-2 bg-primary-500"
      aria-label="Save changes"
      role="button"
      tabIndex={0}
    >
      {children}
    </button>
  )
}
```

### Linting and Formatting Standards

- All code must pass ESLint and Prettier checks before commit
- Maximum line length of 80 characters
- Tab indentation (4 spaces)
- Single quotes for strings
- Trailing commas in objects and arrays
- Parentheses around arrow function parameters
- Single attribute per line in JSX

```typescript
// Don't
const Component = ({name, title, description}) => <div className="container" onClick={handleClick}>
  {name}
</div>

// Do
const Component = ({ 
  name,
  title,
  description 
}: ComponentProps): JSX.Element => (
  <div 
    className='container'
    onClick={handleClick}>
    {name}
  </div>
)
```

### Additional ESLint Rules

- Maximum complexity score of 10 per function
- Maximum 5 parameters per function
- Strict boolean expressions required
- No leaked renders in React components
- Enforced React compiler optimization
- Strict immutable data patterns
- Organized imports with enforced grouping:
  1. Built-in Node modules
  2. External dependencies
  3. Internal modules
  4. Parent directory imports
  5. Sibling files
  6. Index files

```typescript
// Correct import ordering
import { useState } from 'react'

import { axios } from 'axios'
import { z } from 'zod'

import { AppConfig } from '@/config'

import { ParentComponent } from '../ParentComponent'

import { siblingUtil } from './utils'

import { Types } from '.'
```

### React-Specific Rules

- Function components must use function declarations
- Maximum JSX nesting depth of 5 levels
- Strict enforcement of React hooks rules
- Exhaustive dependencies in effect hooks
- Proper state management patterns

```typescript
// Don't
const MyComponent = () => {
  useEffect(() => {
    doSomething(value)
  }, []) // Missing dependency

// Do
function MyComponent(): JSX.Element {
  useEffect(() => {
    doSomething(value)
  }, [value]) // All dependencies included
}
```
