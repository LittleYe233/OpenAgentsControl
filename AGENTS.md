# OpenAgentsControl - Agent Development Guide

Essential information for agentic coding agents working in this repository.

## Build & Test Commands

```bash
# Build evaluation framework
npm run dev:build  # or: cd evals/framework && npm run build

# Run tests
npm test                              # All tests
make test-smoke                       # Quick smoke test (1 test, ~30s)
make test-golden                      # Golden tests (8 tests, ~3-5 min)
make test-agent AGENT=openagent       # Specific agent
make test-model MODEL=anthropic/claude-3-5-sonnet-20241022  # Specific model

# Single test by pattern
cd evals/framework && npm run eval:sdk -- --pattern="**/test-name.yaml"

# Linting
cd evals/framework && npm run lint      # Check
cd evals/framework && npm run lint:fix  # Auto-fix
```

## Code Style Guidelines

### TypeScript Configuration
**ES2020, strict mode, ESM modules** - Always use `.js` extensions in imports
```typescript
// Imports: External first, then internal (with .js extension)
import { describe, it, expect } from 'vitest';
import { ClientManager } from './client-manager.js';
import type { TestCase } from './test-case-schema.js';
```

### Type Safety
- **NEVER use `any`** - Use explicit types or `unknown` with type guards
- **Interfaces** for object shapes, `type` for unions/intersections
- **Branded types** for runtime validation (e.g., Zod schemas)
```typescript
// ✅ GOOD - Explicit types
interface User { id: string; name: string; }
type Result<T> = { success: true; value: T } | { success: false; error: Error };

// ❌ AVOID - Any type
function processData(data: any): any { ... }
```

### Naming Conventions
- **Functions**: Prefer single-word (`create()`, `fork()`, `get()`)
- **Files**: `lowercase-with-dashes.js` (test-runner.ts)
- **Classes**: PascalCase (`TestExecutor`, `ApprovalStrategy`)
- **Constants**: UPPER_SNAKE_CASE (`DEFAULT_TIMEOUT`, `MAX_RETRIES`)

### Code Patterns

**Pure Functions** - Same input = same output, no side effects
```typescript
// ✅ Pure function
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}
// ❌ Side effects
let total = 0;
function addToTotal(item: Item) { total += item.price; }  // Mutates external state
```

**Immutability** - Create new data, don't modify
```typescript
// ✅ Immutable
const addItem = (items: Item[], item: Item): Item[] => [...items, item];
// ❌ Mutable
const addItem = (items: Item[], item: Item) => { items.push(item); return items; };
```

**Early Returns** - Reduce nesting
```typescript
// ✅ Early returns
function process(user: User | null): Result {
  if (!user) return { success: false, error: new Error('User not found') };
  if (!user.isActive) return { success: false, error: new Error('User inactive') };
  return { success: true, value: processUser(user) };
}
// ❌ Deep nesting (avoid)
```

### Error Handling

**ALWAYS** handle errors gracefully with specific error types
```typescript
try {
  const result = await riskyOperation();
  return { success: true, value: result };
} catch (error) {
  if (error instanceof ValidationError) return { success: false, error: new Error('Invalid input') };
  if (error instanceof NetworkError) return { success: false, error: new Error('Network failed') };
  // Never expose internal details to users
  return { success: false, error: new Error('Operation failed') };
}
```

**NEVER**: Expose sensitive info (passwords, tokens, API keys), log credentials, skip input validation

### Testing

**AAA Pattern** (Arrange-Act-Assert) with Vitest
```typescript
describe('TestRunner', () => {
  it('should execute test case', async () => {
    // Arrange
    const testCase = createMockTestCase();
    const executor = new TestExecutor(...);
    // Act
    const result = await executor.execute(testCase, approvalStrategy);
    // Assert
    expect(result.sessionId).toBeDefined();
    expect(result.errors).toHaveLength(0);
  });
});
```

Test files: `**/__tests__/*.test.ts` or `**/*.spec.ts` • Run: `npm test` or `vitest` • Watch: `npm run test:watch`

## Project Structure

```
OpenAgentsControl/
├── evals/framework/          # Main evaluation framework
│   ├── src/
│   │   ├── sdk/             # Test execution SDK
│   │   ├── evaluators/      # Test evaluators
│   │   ├── collector/       # Session data collectors
│   │   └── logging/         # Logging utilities
│   └── package.json
├── .opencode/               # Agent definitions & contexts
│   ├── agent/               # Agent configurations
│   ├── context/             # Context files (patterns, standards)
│   └── skills/              # Agent skills
└── packages/                # Shared packages
```

## Key Standards

**Core Philosophy**: Modular, Functional, Maintainable
- **Small functions** (< 50 lines), **pure functions** (no side effects), **explicit dependencies** (dependency injection), **composition over inheritance**

**Security**: Use environment variables for secrets (never hardcode), validate all inputs, never expose internal error details, follow principle of least privilege

**Logging**: Debug (development only), Info (milestones), Warning (non-blocking issues), Error (failures/exceptions)

**Important Notes**: This is an **ESM project** - always use `.js` extensions. **No Prettier** - use ESLint for formatting. **TypeScript strict mode** enforced. **Context files** in `.opencode/context/` define project-specific patterns. **Tests use Vitest** with TypeScript via tsx.
