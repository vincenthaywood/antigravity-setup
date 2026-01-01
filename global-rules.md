# GOOGLE ANTI-GRAVITY GLOBAL RULES

Paste this entire file into: **Customizations → Rules → + Global**

---

## 1. MANDATORY CONTEXT LOADING (NEVER SKIP)

### Pre-Coding Checklist
Before writing or modifying ANY code, you MUST read these files IN ORDER:

1. **`/docs/architect.md`** - Architecture decisions & patterns
2. **`/docs/tech-spec.md`** - Technical specifications
3. **`/docs/prd.md`** - Product Requirements (if exists)
4. **`/docs/changelog/pending.md`** - Recent changes
5. **TaskMaster** - Current task assignment (if task-driven)

### Enforcement Rules
- If asked to code without reading these files: Say "Let me load context first" and read them
- If a file doesn't exist: Note it and proceed
- Reference specific sections from docs when explaining decisions
- Cite docs: "Per architect.md, we use Server Actions for mutations..."

### Context Verification
Before coding, confirm you can answer:
- [ ] What's the established pattern for this type of change?
- [ ] Are there existing components/functions to reuse?
- [ ] What was the last change to this area?
- [ ] What are the acceptance criteria?
- [ ] Am I staying within my assigned task scope?

**If you cannot answer these → READ THE DOCS / CHECK TASKMASTER FIRST**

---

## 2. IDENTITY & COMMUNICATION

- **Tone**: Technical, concise, objective
- **Efficiency**: Skip apologies, greetings, meta-commentary
- **Focus**: Code and execution results
- **Documentation**: JSDoc/TSDoc on all exported functions
- **Comments**: Explain "Why", not "What"

---

## 3. SECURITY & BOUNDARIES

### File Access
- FORBIDDEN: Writing/modifying files outside current workspace root

### Credentials
- NEVER hardcode API keys or secrets
- Check for `.env.example` or prompt user
- Service role keys: Server-side only, never expose to client

### Dangerous Commands
Commands requiring EXPLICIT user confirmation:
- `sudo` anything
- `rm -rf` (especially with `/` or `~`)
- System-level configuration changes
- Database drops/truncates
- Production deployments

---

## 4. COGNITIVE STRATEGIES

### Chain of Thought (Complex Tasks)
Before proposing complex solutions, create a `### Thought Process` section:
1. Core technical challenge
2. Potential edge cases
3. Impact on existing architecture
4. Relevant patterns from `/docs/architect.md`

### Red Team Review (Before Completing)
After drafting code, self-review for:
- Inefficiencies (O(n) vs O(log n))
- Security vulnerabilities (OWASP Top 10)
- DRY violations
- Missing error handling
- Missing input validation

### Proactive Inquiry
If task is ambiguous:
- Provide 2 possible interpretations
- Ask for clarification BEFORE executing

### Self-Healing Execution
If a terminal command fails:
1. Analyze the error message
2. Search for fix (use Perplexity MCP if needed)
3. Retry ONCE with fix applied
4. If still failing → Report error and ask for help

---

## 5. TYPESCRIPT STANDARDS

- TypeScript exclusively, strict mode enabled
- Explicit interfaces/types for ALL function parameters and return types
- NEVER use `any` - use `unknown` then narrow
- Named exports for React components (avoid default exports)

### Standard Types
```typescript
type ActionResponse<T> = 
  | { success: true; data: T }
  | { success: false; error: string; code?: string };
```

---

## 6. NEXT.JS APP ROUTER (MANDATORY)

- ALWAYS use App Router (`app/` directory)
- NEVER use `pages/` directory
- Server Components are default
- Use `'use client'` ONLY for useState, useEffect, event handlers, browser APIs
- Put `'use client'` at leaf component level, not file top

### Server Actions
Location: `app/actions.ts` or `app/[feature]/actions.ts`
- All mutations via Server Actions
- Zod validation before processing
- Return `ActionResponse<T>` type

### Route Handlers - ONLY For
- Webhook endpoints (n8n, Stripe, GitHub)
- Public APIs for external services
- File uploads/downloads

---

## 7. INPUT VALIDATION

- ALL inputs validated with Zod schemas
- Schemas location: `/lib/schemas/`
- Validate: route params, query params, form data, API bodies

---

## 8. SUPABASE PATTERNS

- Server Components: `createServerClient` from `@supabase/ssr`
- Client Components: `createBrowserClient` from `@supabase/ssr`
- NEVER expose `service_role` key to client
- Row Level Security (RLS) on ALL tables
- Use `.single()` for one row, `.maybeSingle()` for optional

---

## 9. IMAGES & MEDIA

- NEVER use `<img>` tags
- ALWAYS use `next/image` with explicit width/height
- `priority={true}` ONLY for above-the-fold images

---

## 10. STYLING

- Tailwind CSS exclusively
- No separate `.css` files (except global resets)
- No inline `style` objects
- Primary UI library: shadcn/ui

---

## 11. COMPONENT PATTERNS

### NO Barrel Files
```tsx
// ❌ BAD
export { Button } from './Button';

// ✅ GOOD
import { Button } from '@/components/ui/button';
```

### Suspense Boundaries Required
```tsx
<Suspense fallback={<Skeleton />}>
  <AsyncComponent />
</Suspense>
```

---

## 12. GIT & VERSION CONTROL

### Branch Strategy
- `main` - Production-ready only
- `staging` - Pre-production testing
- `feat/[feature-name]` - New features
- `fix/[issue-number]` - Bug fixes

### Commit Messages
```
feat: add user authentication
fix: resolve payment bug
refactor: improve error handling
docs: update webhook documentation
test: add auth unit tests
```

### Code Hygiene
- No `console.log` or `debugger` in production

---

## 13. MCP SERVER PROTOCOL

**Installed**: Vercel, Notion, Github, Supabase, Context7, Sequential Thinking, Perplexity Ask, TaskMaster AI

### Usage Matrix
| MCP | Use When | Avoid When |
|-----|----------|------------|
| GitHub | Reading issues, creating PRs | Trivial changes |
| Supabase | Querying schema | Simple questions (read docs) |
| Vercel | Deploying, checking logs | Local development |
| Notion | Task notes, documentation | Code-level docs |
| Sequential Thinking | Complex planning | Simple tasks |
| Perplexity Ask | External research | Questions from `/docs/` |
| TaskMaster AI | Task orchestration | Ad-hoc small changes |

### Best Practices
1. Read-Only First: Check before writing
2. Explicit Permission: Ask before destructive actions
3. No Over-Fetching: Only fetch what's needed
4. Error Handling: Report after 2 failures

---

## 14. TASKMASTER INTEGRATION

### Task-Driven Protocol
1. Check: `get_next_tasks`
2. Load: `get_task --id=[task-id]`
3. Verify dependencies complete
4. Set: `set_task_status --id=[task-id] --status=in-progress`
5. Work ONLY within task scope
6. Complete: `set_task_status --id=[task-id] --status=done`

### Scope Creep Protocol
If you discover work outside your task:
1. DO NOT just do it
2. Note in current task
3. Create: `add_subtask --parent=[id] --title="[work]"`
4. Continue original task

### Agent Boundaries (Multi-Agent)
| Agent | File Domains |
|-------|--------------|
| Backend | `app/actions.ts`, `app/api/`, `lib/` |
| Frontend | `app/**/page.tsx`, `components/` |
| Testing | `tests/` |
| Docs | `docs/` |

---

## 15. DOCUMENTATION PROTOCOL

### Auto-Changelog
Every change logged to `/docs/changelog/pending.md`:
```
[HH:MM] - [FILE] - [CHANGE] - [WHY]
```

When 5+ entries → Run `/consolidate`

---

## 16. MANDATORY ARTIFACTS

Every task completion generates:
1. Task Summary
2. Implementation Notes
3. Testing Instructions
4. Changelog Entry (via `/log`)
5. TaskMaster Update (if task-driven)

---

## 17. DEPENDENCIES TO AVOID

| ❌ Avoid | ✅ Use Instead |
|----------|----------------|
| moment.js | date-fns |
| lodash | Native JS or lodash-es |
| axios | Native fetch |

---

## 18. CODE STATUS INDICATORS

```typescript
// TODO: Standard task
// FIXME: Bug needing attention
// HACK: Temporary workaround
// @vincent: Requires your decision
// TASK-[ID]: Links to TaskMaster
```

---

## 19. PERFORMANCE TARGETS

| Metric | Target |
|--------|--------|
| Bundle Size | < 200KB |
| LCP | < 2.5s |
| CLS | < 0.1 |
| FID | < 100ms |

---

## 20. WORKFLOW REFERENCE

| Workflow | Purpose |
|----------|---------|
| `/context` | Load session context |
| `/task` | Execute TaskMaster task |
| `/breakdown` | Feature → tasks |
| `/status` | Project progress |
| `/log` | Log changes |
| `/consolidate` | Clean changelog |
| `/test` | Generate tests |
| `/fix` | Debug bugs |
| `/pr` | Prepare PR |
| `/docs` | Update docs |
| `/perf` | Performance audit |
| `/plan` | Plan features |
| `/webhook` | Document webhooks |
| `/setup` | New project setup |
