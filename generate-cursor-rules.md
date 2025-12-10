# Task:  Analyze this codebase and generate a hierarchical `.cursor/rules/*. mdc` system

## Critical Context:  Cursor Rules Are Different

Cursor rules have unique characteristics: 
1. **Scoped via Globs**: Rules are auto-attached based on file path patterns
2. **YAML Front-matter Required**: `.mdc` files use metadata for targeting
3. **Hierarchical Priority**: `alwaysApply:  true` rules load globally; others load contextually
4. **Token Efficiency**: Rules should be actionable, not encyclopedic
5. **JIT Context**:  Provide paths/patterns/commands, NOT exhaustive documentation

## Core Principles

1. **00-core. mdc is LIGHTWEIGHT** - Only universal guidance (~50-100 lines)
2. **Module rules are SPECIFIC** - Detailed patterns, examples, boundaries
3. **Nearest-wins context** - Cursor reads the most relevant rule based on current file
4. **Real examples only** - Every pattern must reference actual files in the codebase
5. **Copy-paste commands** - No placeholders, runnable as-is
6. **Detect, don't assume** - Use the project's actual package manager, not a default

---

## Your Process

### Phase 1: Repository Analysis

First, analyze the codebase and provide: 

**1. Package Manager Detection** ⚠️ CRITICAL
Detect the package manager by checking for lock files and config: 

| Lock File | Config | Package Manager | Run Command | Filter Syntax |
|-----------|--------|-----------------|-------------|---------------|
| `pnpm-lock. yaml` | `pnpm-workspace.yaml` | pnpm | `pnpm` | `pnpm --filter <pkg>` |
| `package-lock.json` | - | npm | `npm run` | `npm --workspace <pkg> run` |
| `yarn.lock` | `.yarnrc.yml` (v2+) | yarn | `yarn` | `yarn workspace <pkg>` |
| `bun.lockb` or `bun.lock` | `bunfig.toml` | bun | `bun` | `bun --filter <pkg>` |
| `deno.lock` | `deno.json` | deno | `deno task` | N/A |

**Store detected values:**
```
PACKAGE_MANAGER: [npm|pnpm|yarn|bun|deno]
RUN_CMD: [npm run|pnpm|yarn|bun|deno task]
INSTALL_CMD: [npm install|pnpm install|yarn|bun install|deno install]
FILTER_CMD: [npm --workspace|pnpm --filter|yarn workspace|bun --filter]
EXEC_CMD: [npx|pnpm dlx|yarn dlx|bunx|deno run]
```

**2. Repository Architecture**
- Type:  Monorepo (workspaces)? Multi-package? Standard single project?
- Workspace configuration: Check `package.json` workspaces field, `pnpm-workspace.yaml`, etc.
- Build system:  Turborepo?  Nx? Lerna?  Simple scripts? 

**3. Technology Stack**
- Languages: TypeScript strict mode?  JavaScript? 
- Frameworks:  React, Next.js, Express, Hono, Fastify?
- ORM/Database: Drizzle, Prisma, TypeORM, raw SQL?
- Testing:  Vitest, Jest, Playwright? 
- Styling: Tailwind, CSS Modules, styled-components? 

**4. Module/Package Map**
Identify all directories that need dedicated rules:
```
apps/
  ├── web/          → Frontend app (Next.js/React)
  ├── api/          → Backend API (Express/Hono)
  └── mobile/       → Mobile app (React Native)
packages/
  ├── ui/           → Shared UI components
  ├── shared/       → Shared utilities/types
  └── db/           → Database schema/client
services/
  └── auth/         → Auth microservice
```

**5. Import Boundaries**
Map allowed/forbidden import relationships:
- `apps/web` → CAN import from `packages/ui`, `packages/shared`
- `apps/web` → CANNOT import from `apps/api`, `services/*`
- `packages/ui` → CANNOT import from `apps/*`

**6. Key Patterns to Document**
For each module, identify:
- Entry points (main files)
- Example files that demonstrate best practices
- Anti-patterns to avoid (with actual file references if they exist)
- Critical conventions specific to that module

**7. Dangerous Operations**
- Commands that should never run automatically
- Files that should never be edited directly
- Imports that violate module boundaries

Present this as a **structured map** before generating any files. 

---

### Phase 2: Generate Core Rules (`00-core.mdc`)

Create a lightweight global rules file (~50-100 lines).

**Use detected package manager commands throughout! **

Example for **pnpm**: 
```yaml
---
description: "Project-wide standards applied to all files"
globs: []
alwaysApply:  true
---

# [Project Name] - Global Standards

## Tech Stack
- **Runtime**: Node.js 20+ / Bun 1.x
- **Language**: TypeScript (strict mode enabled)
- **Package Manager**: pnpm with workspaces
- **Monorepo Tool**: Turborepo

## Universal Commands
```bash
pnpm install          # Install all dependencies
pnpm dev              # Start all dev servers
pnpm build            # Build all packages
pnpm test             # Run all tests
pnpm typecheck        # TypeScript validation
pnpm lint             # ESLint all code
pnpm lint:fix         # Auto-fix linting issues
```
```

Example for **npm workspaces**: 
```yaml
## Universal Commands
```bash
npm install           # Install all dependencies
npm run dev           # Start all dev servers
npm run build         # Build all packages
npm run test          # Run all tests
npm run typecheck     # TypeScript validation
npm run lint          # ESLint all code
npm run lint: fix      # Auto-fix linting issues
```
```

Example for **yarn**:
```yaml
## Universal Commands
```bash
yarn                  # Install all dependencies
yarn dev              # Start all dev servers
yarn build            # Build all packages
yarn test             # Run all tests
yarn typecheck        # TypeScript validation
yarn lint             # ESLint all code
yarn lint:fix         # Auto-fix linting issues
```
```

Example for **bun**:
```yaml
## Universal Commands
```bash
bun install           # Install all dependencies
bun dev               # Start all dev servers
bun run build         # Build all packages
bun test              # Run all tests
bun run typecheck     # TypeScript validation
bun run lint          # ESLint all code
bun run lint:fix      # Auto-fix linting issues
```
```

#### Complete Core Template (adapt commands to detected PM):

```yaml
---
description: "Project-wide standards applied to all files"
globs: []
alwaysApply: true
---

# [Project Name] - Global Standards

## Tech Stack
- **Runtime**:  [Node.js X / Bun X]
- **Language**:  TypeScript (strict mode enabled)
- **Package Manager**: [DETECTED_PM] [with workspaces if monorepo]
- **Monorepo Tool**: [Turborepo/Nx/None]

## Universal Commands
```bash
[INSTALL_CMD]         # Install all dependencies
[RUN_CMD] dev         # Start all dev servers
[RUN_CMD] build       # Build all packages
[RUN_CMD] test        # Run all tests
[RUN_CMD] typecheck   # TypeScript validation
[RUN_CMD] lint        # ESLint all code
[RUN_CMD] lint: fix    # Auto-fix linting issues
```

## Code Quality Rules

### MUST
- Use TypeScript strict mode (no `any` without justification)
- Write tests for all new features
- Use named exports over default exports
- Keep functions under 50 lines
- Use descriptive variable names

### MUST NOT
- Commit secrets, API keys, or tokens
- Use `@ts-ignore` or `@ts-expect-error` without comment
- Push directly to `main` branch
- Use `console.log` in production code (use logger)

## Git Conventions
- Branch:  `feature/`, `fix/`, `chore/`, `docs/`
- Commits:  Conventional Commits (`feat: `, `fix:`, `docs:`)
- PRs: Require passing CI + 1 approval

## Import Boundaries
- Apps can import from packages, not from other apps
- Packages can import from other packages, not from apps
- Use `@/` alias for absolute imports within a package

## Security
- Never commit `.env` files (use `.env.example`)
- Use environment variables for secrets
- Validate all external input with Zod/Valibot

## Quick Find Commands
```bash
# Find a function
rg -n "export (async )?function \w+" --type ts

# Find a React component
rg -n "export (function|const) [A-Z]" --type tsx

# Find API routes
rg -n "export (async )?function (GET|POST|PUT|DELETE)" apps/

# Find type definitions
rg -n "^export (type|interface)" packages/
```
```

---

### Phase 3: Generate Module-Specific Rules

For EACH module/package identified in Phase 1, create a detailed `.mdc` file.

**Always use the detected package manager's syntax!**

#### Template with Package Manager Variables: 

```yaml
---
description: "[Brief purpose of this module]"
globs: 
  - "apps/web/**"
  - "! apps/web/node_modules/**"
alwaysApply: false
---

# [Module Name] Rules

## Purpose
[2-3 sentences describing what this module does]

## Tech Stack (This Module)
- **Framework**: [e.g., Next.js 14 with App Router]
- **State**:  [e.g., Zustand, React Query]
- **Styling**: [e.g., Tailwind CSS]
- **Testing**: [e.g., Vitest + Testing Library]

## Commands

### From this directory
```bash
[RUN_CMD] dev         # Start dev server (localhost:3000)
[RUN_CMD] build       # Production build
[RUN_CMD] test        # Run tests
[RUN_CMD] typecheck   # Type check
```

### From root (monorepo)
```bash
# pnpm
pnpm --filter @repo/web dev
pnpm --filter @repo/web test

# npm workspaces
npm --workspace @repo/web run dev
npm --workspace @repo/web run test

# yarn
yarn workspace @repo/web dev
yarn workspace @repo/web test

# bun
bun --filter @repo/web dev
bun --filter @repo/web test
```
> ⚠️ Use ONLY the commands matching your detected package manager

## Directory Structure
```
src/
├── app/              # Next.js App Router pages
├── components/       # React components
│   ├── ui/          # Atomic UI components
│   └── features/    # Feature-specific components
├── hooks/           # Custom React hooks
├── lib/             # Utilities and helpers
├── stores/          # Zustand stores
└── types/           # TypeScript definitions
```

## Code Patterns

### Components
✅ **DO**:  Functional components with TypeScript
   Example: `src/components/ui/Button.tsx`

✅ **DO**: Co-locate tests with components
   Example: `src/components/ui/Button.test. tsx`

❌ **DON'T**: Class components
   Legacy:  `src/legacy/OldComponent.tsx` (do not copy)

### State Management
✅ **DO**: Zustand for global state
   Example: `src/stores/userStore.ts`

✅ **DO**: React Query for server state
   Example:  `src/hooks/useUsers.ts`

### Data Fetching
✅ **DO**:  Server Components for initial data
   Example:  `src/app/users/page.tsx`

✅ **DO**: Client hooks for mutations
   Example: `src/hooks/useCreateUser.ts`

### Forms
✅ **DO**: React Hook Form + Zod
   Example: `src/components/features/auth/LoginForm.tsx`

## Import Rules

### Allowed
- `@repo/ui` - Shared UI components
- `@repo/shared` - Shared utilities
- `@/` - Absolute imports within this package

### Forbidden
- `@repo/api` - Backend code
- `../../../` - Deep relative imports (use `@/`)
- Direct database imports

## Key Files
- `src/app/layout.tsx` - Root layout, providers
- `src/lib/api.ts` - API client
- `src/types/index.ts` - Shared types
- `src/middleware.ts` - Auth middleware

## Common Gotchas
- Client components need `"use client"` directive
- Environment variables for client need `NEXT_PUBLIC_` prefix
- Dynamic route params are async in Next.js 15+
- Always use `@/` imports, never deep relative paths

## AI Hints
- Prefer Server Components unless interactivity needed
- Use Suspense boundaries for loading states
- Check `src/components/ui/` before creating new base components
- Follow existing patterns in `src/components/features/`

## Pre-PR Checklist
```bash
# pnpm
pnpm --filter @repo/web typecheck && \
pnpm --filter @repo/web lint && \
pnpm --filter @repo/web test && \
pnpm --filter @repo/web build

# npm workspaces
npm --workspace @repo/web run typecheck && \
npm --workspace @repo/web run lint && \
npm --workspace @repo/web run test && \
npm --workspace @repo/web run build

# yarn
yarn workspace @repo/web typecheck && \
yarn workspace @repo/web lint && \
yarn workspace @repo/web test && \
yarn workspace @repo/web build

# bun
bun --filter @repo/web typecheck && \
bun --filter @repo/web lint && \
bun --filter @repo/web test && \
bun --filter @repo/web build
```
> ⚠️ Use ONLY the commands matching your detected package manager
```

---

### Phase 4: Special Module Types

#### For API/Backend Modules: 

```yaml
---
description: "Backend API service rules"
globs: 
  - "apps/api/**"
alwaysApply:  false
---

# API Module Rules

## Purpose
Backend REST/GraphQL API serving the frontend applications. 

## Tech Stack
- **Framework**:  Hono / Express
- **Database**: PostgreSQL via Drizzle ORM
- **Validation**: Zod schemas
- **Auth**: JWT with refresh tokens

## Commands
```bash
[RUN_CMD] dev              # Start with hot reload
[RUN_CMD] build            # Compile TypeScript
[RUN_CMD] db:migrate       # Run migrations
[RUN_CMD] db:seed          # Seed database
[RUN_CMD] db:studio        # Open Drizzle Studio
```

## Code Patterns

### Route Handlers
✅ **DO**:  Zod validation on all inputs
   Example: `src/routes/users/create.ts`

✅ **DO**: Error handling with custom ApiError
   Example:  `src/lib/errors.ts`

### Database Access
✅ **DO**: Use Drizzle ORM, never raw SQL
   Example: `src/db/queries/users.ts`

✅ **DO**:  Transactions for multi-step operations
   Example: `src/services/order. ts`

❌ **DON'T**: Database queries in route handlers
   Move to:  `src/db/queries/` or `src/services/`

### Authentication
✅ **DO**: Apply auth middleware to protected routes
   Example:  `src/middleware/auth.ts`

## Import Rules

### Allowed
- `@repo/shared` - Shared types and utilities
- `@repo/db` - Database schema and client

### Forbidden
- `@repo/ui` - Frontend components
- `@repo/web` - Frontend app code

## Security
- Validate ALL input with Zod schemas
- Never expose internal errors to clients
- Use parameterized queries (Drizzle handles this)
- Rate limit public endpoints
- Log security events

## Database Conventions
- Schema: `src/db/schema/`
- Migrations: `drizzle/migrations/`
- Never edit existing migrations
- Test migrations on fresh DB before committing
```

#### For UI/Design System Packages:

```yaml
---
description: "Shared UI component library rules"
globs: 
  - "packages/ui/**"
alwaysApply: false
---

# UI Package Rules

## Purpose
Shared, reusable UI components used across all frontend apps.

## Tech Stack
- **Components**: React with TypeScript
- **Styling**: Tailwind CSS + CVA (class-variance-authority)
- **Testing**: Vitest + Testing Library
- **Docs**:  Storybook

## Commands
```bash
[RUN_CMD] dev              # Watch mode build
[RUN_CMD] build            # Production build
[RUN_CMD] storybook        # Open Storybook
[RUN_CMD] test             # Run component tests
```

## Component Structure
```
src/components/
├── Button/
│   ├── Button.tsx         # Component
│   ├── Button.test.tsx    # Tests
│   ├── Button.stories.tsx # Storybook
│   └── index.ts           # Export
```

## Code Patterns

### Components
✅ **DO**: Use CVA for variants
   Example: `src/components/Button/Button.tsx`

✅ **DO**: Forward refs for DOM elements
   Example:  `src/components/Input/Input.tsx`

✅ **DO**:  Accessible by default (ARIA, keyboard)
   Example: `src/components/Modal/Modal.tsx`

### Styling
✅ **DO**: Use design tokens from `src/tokens. ts`
❌ **DON'T**: Hardcode colors, spacing, fonts

### Exports
✅ **DO**: Named exports only
✅ **DO**:  Re-export from `src/index.ts`

## Import Rules

### Allowed
- `@repo/shared` - Shared utilities

### Forbidden
- `@repo/web` - App-specific code
- `@repo/api` - Backend code
- Any database imports

## AI Hints
- Check existing components before creating new ones
- Every component needs a Storybook story
- Components must be accessible (a11y)
- Use `cn()` helper for conditional classes
```

---

## Package Manager Reference Card

Include this reference when generating rules: 

| Action | npm | pnpm | yarn | bun |
|--------|-----|------|------|-----|
| Install | `npm install` | `pnpm install` | `yarn` | `bun install` |
| Run script | `npm run <script>` | `pnpm <script>` | `yarn <script>` | `bun <script>` |
| Run in workspace | `npm -w <pkg> run <script>` | `pnpm --filter <pkg> <script>` | `yarn workspace <pkg> <script>` | `bun --filter <pkg> <script>` |
| Execute package | `npx <pkg>` | `pnpm dlx <pkg>` | `yarn dlx <pkg>` | `bunx <pkg>` |
| Add dependency | `npm install <pkg>` | `pnpm add <pkg>` | `yarn add <pkg>` | `bun add <pkg>` |
| Add dev dep | `npm install -D <pkg>` | `pnpm add -D <pkg>` | `yarn add -D <pkg>` | `bun add -D <pkg>` |
| Remove dep | `npm uninstall <pkg>` | `pnpm remove <pkg>` | `yarn remove <pkg>` | `bun remove <pkg>` |

---

## Output Format

Provide files in this order:

1. **Analysis Summary** (Phase 1 findings, including detected package manager)
2. **Rule File Structure** (directory tree)
3. **Each `.mdc` file** with full path

Format:
```
---
File: `.cursor/rules/00-core.mdc`
Purpose: Global project standards
Package Manager: [DETECTED]
---
[complete file content with correct PM commands]

---
File: `.cursor/rules/web. mdc`
Purpose: Frontend Next.js application rules
---
[complete file content with correct PM commands]
```

---

## Quality Checklist

Before generating, verify:

- [ ] Package manager correctly detected from lock files
- [ ] ALL commands use the detected package manager syntax
- [ ] `00-core.mdc` is under 100 lines
- [ ] Module rules reference REAL files from the codebase
- [ ] Every "✅ DO" has an actual file example path
- [ ] Every "❌ DON'T" references real anti-patterns or clearly states what to avoid
- [ ] Commands are copy-paste ready (no placeholders like `[RUN_CMD]`)
- [ ] Globs correctly target the module (include negation for node_modules)
- [ ] Import boundaries are clearly defined
- [ ] No duplication between core and module rules
- [ ] Each rule file is focused and actionable
- [ ] AI hints are specific to the module context
- [ ] Filter/workspace commands match the package manager

---

## Start Here

Begin by analyzing the codebase: 

1. **First, detect the package manager** by checking for: 
   - `pnpm-lock.yaml` → pnpm
   - `package-lock.json` → npm
   - `yarn.lock` → yarn
   - `bun.lockb` or `bun.lock` → bun
   - `deno.lock` → deno

2. Then present **Phase 1 (Repository Analysis)** as a structured map. 

Ask clarifying questions about:
- Preferred testing strategy (unit vs integration vs e2e)
- Any legacy patterns that should be explicitly warned against
- Critical import boundaries that must never be violated
- Specific conventions the team follows (naming, file organization)
- Known gotchas or foot-guns in the codebase
- Which modules are most actively developed

Then generate the complete `.cursor/rules/` structure using the **detected package manager's commands**. 
