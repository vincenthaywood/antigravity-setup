# [PROJECT NAME] WORKSPACE RULES

Customize this template, then paste into: **Customizations → Rules → + Workspace**

---

## Project Context

- **Project**: [Name]
- **Type**: [Web App / API / Tool / Client Work]
- **Stack**: Next.js 15, React 19, TypeScript, Tailwind, Supabase
- **Status**: [Development / Testing / Production]
- **Client**: [If applicable]

---

## Quick Context Load

BEFORE ANY CODING, read in order:
1. `/docs/architect.md` - Architecture decisions
2. `/docs/tech-spec.md` - Technical specs
3. `/docs/prd.md` - Product requirements
4. `/docs/changelog/pending.md` - Recent changes

Or run `/context` workflow.

---

## Project-Specific Patterns

### [Pattern 1]
[Description]

### [Pattern 2]
[Description]

---

## Color System

- Primary: [color]
- Secondary: [color]
- Defined in: `tailwind.config.ts`

---

## Current Focus

**Feature**: [Current feature]
**Acceptance Criteria**: See `/docs/prd.md`

---

## Known Issues / Tech Debt

- [ ] [Issue 1]
- [ ] [Issue 2]

---

## External Integrations

- **n8n**: [Yes/No - describe webhooks]
- **Stripe**: [Yes/No]
- **Other**: [List]

---

## Deployment

- **Hosting**: Vercel
- **Database**: Supabase ([project-id])
- **Env Vars**: Vercel dashboard + `.env.local`
