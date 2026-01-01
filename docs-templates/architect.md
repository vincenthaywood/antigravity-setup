# Architecture Overview

## Project Information

**Project Name**: [Name]
**Owner**: Vincent
**Created**: [Date]
**Last Updated**: [Date]

---

## High-Level Architecture

### System Components
1. **Frontend**: Next.js 15 (App Router)
2. **Backend**: Server Actions + API Routes (webhooks only)
3. **Database**: Supabase (PostgreSQL)
4. **Auth**: Supabase Auth
5. **Styling**: Tailwind CSS + shadcn/ui

### Architecture Diagram
```
┌─────────────────────────────────────────────────────┐
│                    Frontend                          │
│              Next.js App Router                      │
│         (Server Components default)                  │
└─────────────────┬───────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────┐
│              Server Actions                          │
│         (Mutations & Form Handling)                  │
└─────────────────┬───────────────────────────────────┘
                  │
                  ▼
┌─────────────────────────────────────────────────────┐
│                 Supabase                             │
│     PostgreSQL + Auth + Realtime + Storage          │
└─────────────────────────────────────────────────────┘
```

---

## Key Architectural Decisions

### Decision 1: Server Components Default
- **Decision**: Use Server Components unless interactivity required
- **Reason**: Reduces client JavaScript
- **Exception**: `'use client'` for hooks/event handlers

### Decision 2: Server Actions for Mutations
- **Decision**: All mutations via Server Actions
- **Location**: `app/actions.ts` or `app/[feature]/actions.ts`
- **Pattern**: Zod validation → Process → Return `ActionResponse<T>`

### Decision 3: Route Handlers for Webhooks Only
- **Decision**: API routes only for external webhooks
- **Location**: `/app/api/webhooks/[service]/route.ts`

### Decision 4: No Barrel Files
- **Decision**: Never create `index.ts` re-exports
- **Reason**: Breaks tree-shaking

---

## File Structure

```
project-root/
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── actions.ts
│   ├── (auth)/
│   └── api/webhooks/
├── components/
│   └── ui/
├── lib/
│   ├── supabase/
│   └── schemas/
├── tests/
├── docs/
│   ├── architect.md
│   ├── tech-spec.md
│   ├── prd.md
│   └── changelog/
└── .antigravityignore
```

---

## Constraints

- [ ] App Router only (no pages/)
- [ ] No default exports
- [ ] No `any` types
- [ ] No console.log in production
- [ ] No barrel files

---

## Links to Key Files

- `/app/actions.ts` - Server Actions
- `/lib/supabase/server.ts` - Database
- `/app/layout.tsx` - Root layout
- `tailwind.config.ts` - Styling
