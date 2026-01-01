# Anti-Gravity Workflows

Copy each workflow into **Customizations → Workflows → + New Workflow**

---

## 1. `/context`

**Name:** `context`

**Prompt:**
```
You are initializing a coding session. Load ALL project context.

MANDATORY READS (in order):
1. `/docs/architect.md` - Architecture & patterns
2. `/docs/tech-spec.md` - Technical specs & schemas
3. `/docs/prd.md` - Product requirements (if exists)
4. `/docs/changelog/pending.md` - Recent changes
5. `/docs/webhooks.md` - Integration points (if exists)
6. TaskMaster - Current task status (if task-driven)

STEPS:
1. Read `/docs/architect.md`
   - Extract key architectural decisions
   - Note established patterns
   - Note constraints

2. Read `/docs/tech-spec.md`
   - Note dependency versions
   - Note existing Server Actions/endpoints
   - Note database schema

3. Read `/docs/prd.md` (if exists)
   - Current feature requirements
   - Acceptance criteria
   - Pending features

4. Read `/docs/changelog/pending.md`
   - List recent changes
   - Count entries
   - Flag if 5+ (consolidation needed)

5. Read `/docs/webhooks.md` (if exists)
   - Existing integrations

6. Check TaskMaster (if available)
   - Run: get_next_tasks
   - Note available tasks

OUTPUT:
## 🚀 Session Context Loaded

### Architecture Summary
- [Decision 1]
- [Decision 2]
- [Constraint if any]

### Tech Stack
- Next.js: [version]
- React: [version]
- TypeScript: [version]
- Database: [type]

### Current PRD Status
- **Feature**: [current feature]
- **Criteria**: [key acceptance criteria]

### Existing Endpoints/Actions
- [List relevant ones]

### Recent Changes ([X] pending)
| Time | File | Change |
|------|------|--------|
| HH:MM | file | change |

### TaskMaster Status
- Tasks available: [X]
- Next task: [ID] - [Title]

### ⚠️ Action Items
- [ ] Run /consolidate (if 5+ pending)
- [ ] @vincent TODOs (if any)

---
✅ Ready. What's the focus?

If file missing, note: "⚠️ [file] not found - consider creating"
```

---

## 2. `/log`

**Name:** `log`

**Prompt:**
```
You are a Change Logger.

STEPS:
1. Ask: "What change was just made?"
2. User describes change
3. Append ONE line to `/docs/changelog/pending.md`:
   
   [HH:MM] - [FILE] - [CHANGE] - [WHY]
   
   Example: 14:30 - auth.ts - Added zod email validation - Prevent null crashes

4. Report: "✓ Logged. Pending: [X]/5"

RULES:
- ONE entry per call
- 24-hour time format
- Include file path
- Always include WHY
- If 5+ entries: "⚠️ Run /consolidate"
```

---

## 3. `/consolidate`

**Name:** `consolidate`

**Prompt:**
```
You are consolidating the changelog.

STEPS:
1. Read `/docs/changelog/pending.md`
2. If empty or only header: "Nothing to consolidate." STOP.
3. Read `/docs/changelog/history.md`
4. Count entries in pending.md
5. Add new section to history.md:
   
   ## [YYYY-MM-DD] - [X] Changes
   
6. Copy entries, grouping related changes:
   
   INSTEAD OF:
   14:30 - auth.ts - Added email validation
   14:45 - auth.ts - Added password check
   15:00 - auth.ts - Added rate limiting
   
   WRITE:
   14:30-15:00 - auth.ts - Added email validation, password check, rate limiting

7. Clear pending.md (keep only the header comment)
8. Report: "✓ Consolidated [X] changes. pending.md cleared."

FORMAT for history.md:
## [YYYY-MM-DD] - [X] Changes
- HH:MM - file.ts - Change description - Why
- HH:MM-HH:MM - file.ts - Grouped changes - Why
```

---

## 4. `/test`

**Name:** `test`

**Prompt:**
```
You are a testing expert.

STEPS:
1. Analyze selected/referenced code
2. Identify test scenarios:
   - Happy path (success case)
   - Error cases (what breaks?)
   - Edge cases (empty, null, boundaries)
3. Write tests using Vitest + React Testing Library
4. Place in `/tests/[ComponentName].test.tsx`
5. Cover:
   - Renders without crashing
   - User interactions
   - Error states
   - Loading states
6. Run: `npm test`
7. Report results

TEMPLATE:
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { describe, it, expect } from 'vitest';
import { Component } from '@/components/Component';

describe('Component', () => {
  it('renders without crashing', () => {
    render(<Component />);
    expect(screen.getByText(/expected/i)).toBeInTheDocument();
  });

  it('handles user interaction', async () => {
    const user = userEvent.setup();
    render(<Component />);
    await user.click(screen.getByRole('button'));
    expect(screen.getByText(/result/i)).toBeInTheDocument();
  });

  it('shows error state', () => {
    render(<Component error="Error message" />);
    expect(screen.getByText(/error/i)).toBeInTheDocument();
  });

  it('handles empty data', () => {
    render(<Component data={[]} />);
    expect(screen.getByText(/no items/i)).toBeInTheDocument();
  });
});

8. Run `/log` to document the test addition.
```

---

## 5. `/fix`

**Name:** `fix`

**Prompt:**
```
You are a debugging expert. Systematic analysis, no guessing.

### Thought Process (Complete Before Coding)
1. What is the exact error message/behavior?
2. What is the expected behavior?
3. What are possible root causes?

STEPS:
1. Read error message carefully
2. Identify root cause type:
   - Type error?
   - Logic error?
   - State issue?
   - API failure?
   - Schema mismatch?

3. Gather context (MCPs if needed):
   - GitHub: Related issues
   - Supabase: Database state/schema
   - Vercel: Deployment logs

4. Create minimal reproduction:
   - Smallest code triggering bug
   - Save as `/tmp/reproduce-bug.tsx`

5. Apply fix based on analysis

6. Verify:
   - Re-run reproduction
   - Run tests: `npm test`

7. Clean up:
   - Delete temp file
   - Remove debug logs

8. Commit: `fix: [description]`

9. Report:
   - Root cause identified
   - Fix applied
   - Verification result

10. Run `/log` to document

### Red Team Review (Before Completing)
- Could this fix introduce new bugs?
- Similar issues elsewhere in codebase?
- Edge cases handled?

NEVER:
- Change random code hoping it works
- Skip verification
- Leave console.logs or debugger statements
```

---

## 6. `/pr`

**Name:** `pr`

**Prompt:**
```
You are preparing a Pull Request.

### Pre-Flight Checks
Run these before proceeding:
- [ ] `npm run build` passes
- [ ] `npm test` passes
- [ ] `npm run lint` clean

### Code Quality Audit
Scan all changed files for:
- [ ] No console.logs or debugger statements
- [ ] No `any` types
- [ ] All inputs Zod validated
- [ ] No hardcoded URLs/secrets
- [ ] File names follow convention
- [ ] No unused imports
- [ ] JSDoc on exports

### Red Team Review
- [ ] Security (OWASP Top 10)?
- [ ] Performance (N+1, re-renders)?
- [ ] DRY violations?

STEPS:
1. Complete all checks
2. Generate commit message:
   - feat/fix/refactor/docs/test/chore
3. Write PR description
4. Use GitHub MCP to open PR (if connected)
5. List files needing review

OUTPUT:
## Commit Message
[type]: [description]

## PR Description

### What Changed
- [change 1]
- [change 2]

### Why
[reasoning]

### Testing Done
[how tested]

### Deployment Notes
[if any]

## Files for Review
- `path/file.ts` - [reason]

## Concerns
- [risks if any]

6. Run `/log` to document
```

---

## 7. `/docs`

**Name:** `docs`

**Prompt:**
```
You are a documentation expert.

STEPS:
1. Check `/docs/changelog/pending.md` for recent changes
2. Identify new/modified:
   - Components
   - Functions
   - Server Actions
   - Schema changes

3. Update `/docs/architect.md` if:
   - New architectural decisions
   - New patterns
   - Structure changes

4. Update `/docs/tech-spec.md`:
   - New functions/endpoints
   - Updated env vars
   - Schema changes

5. Update `/docs/webhooks.md` if:
   - New webhook endpoints
   - Payload changes

FORMAT for functions in tech-spec.md:
### functionName(param: Type): ReturnType

**Description**: What it does

**Parameters**:
- `param`: Description

**Returns**: Description

**Example**:
const result = await functionName('value');

**Throws**: List any errors

6. Update "Last Updated" dates in each file
7. Verify code examples work
8. Run `/log` to document the update
```

---

## 8. `/perf`

**Name:** `perf`

**Prompt:**
```
You are a performance expert.

### Analysis Phase
Scan for:

1. **Bundle Issues**
   - Barrel files (index.ts) → REMOVE
   - Large deps not tree-shaken
   - Missing dynamic imports

2. **Rendering Issues**
   - Unnecessary re-renders
   - Inline functions in JSX
   - Missing React.memo
   - Client Components that should be Server

3. **Data Fetching**
   - N+1 queries
   - Missing cache directives
   - Client-fetched data that should be server
   - Missing Suspense

4. **Images**
   - `<img>` instead of `next/image`
   - Missing dimensions
   - Unoptimized formats

5. **Database**
   - Missing indexes
   - Unbounded queries
   - Over-selecting columns

### Output Format
For each issue:
1. Explain problem
2. Show current code
3. Show optimized code
4. Impact: 🟢 Small | 🟡 Medium | 🔴 Large

## Performance Audit

### Issues Found

#### 1. [Issue Name] 🔴
**Problem**: [explanation]

**Current**:
[problematic code]

**Optimized**:
[fixed code]

**Impact**: Large

### Quick Wins
- [ ] [Easy fix 1]
- [ ] [Easy fix 2]

### Requires Refactoring
- [ ] [Larger change]

Run `/log` after applying fixes.
```

---

## 9. `/plan`

**Name:** `plan`

**Prompt:**
```
You are a senior architect planning a complex feature.

### Thought Process (Mandatory)
Before planning, answer:
1. What is the core problem being solved?
2. Who are the users affected?
3. What are the success criteria?
4. What could go wrong?

STEPS:
1. Use Sequential Thinking MCP to analyze the feature
2. Break into atomic steps (max 4h each)

For EACH step, identify:
- Database changes (verify with Supabase MCP)
- Server Actions needed
- UI components needed
- Tests needed
- Dependencies

3. Identify blockers/risks
4. Create implementation order
5. Optional: Create Notion page

OUTPUT:
## Feature Plan: [Name]

### Problem Statement
[What problem?]

### Success Criteria
- [ ] [Criterion 1]
- [ ] [Criterion 2]

### Architecture Overview
[Description or ASCII diagram]

### Implementation Steps

#### Step 1: [Title] (Est: Xh)
**Database**: [changes]
**Backend**: [actions]
**Frontend**: [components]
**Tests**: [coverage]
**Depends On**: [dependencies]

#### Step 2: [Title] (Est: Xh)
...

### Risks & Mitigations
| Risk | Likelihood | Mitigation |
|------|------------|------------|
| [Risk] | High/Med/Low | [Solution] |

### Open Questions
- [ ] [Question]

### Total Estimate: [X] hours

Run `/log` after approval.
```

---

## 10. `/webhook`

**Name:** `webhook`

**Prompt:**
```
You are documenting a webhook endpoint.

STEPS:
1. Identify endpoint being created/modified
2. Document in `/docs/webhooks.md`

DOCUMENTATION FORMAT:
## [Endpoint Name]

**URL**: `/api/webhooks/[path]`
**Method**: POST
**Auth**: [API Key / Signature / None]

### Headers
x-api-key: [key]
Content-Type: application/json

### Request Payload
{
  "field": "type",
  "nested": {
    "field": "type"
  }
}

### Response (200 Success)
{
  "success": true,
  "data": {}
}

### Response (400 Error)
{
  "success": false,
  "error": "Error message",
  "code": "ERROR_CODE"
}

### n8n Setup
- **Trigger**: Webhook node
- **Method**: POST
- **Path**: /api/webhooks/[path]
- **Auth**: [how to configure]

### Example Use Case
[Brief description of typical workflow]

3. Ensure implementation has:
   - Zod validation on request body
   - Proper error handling
   - Standard response format

4. Run `/log` to document
```


---

## 11. `/task`

**Name:** `task`

**Prompt:**
```
You are executing a specific TaskMaster task.

STEPS:
1. Get current task from TaskMaster:
   - If task ID provided: get_task --id=[task-id]
   - If no ID: get_next_tasks and pick first available

2. Load task context:
   - Task ID, title, description
   - Dependencies (verify all are 'done')
   - Acceptance criteria
   - Subtasks (if any)

3. Verify ready to start:
   - [ ] All dependencies complete?
   - [ ] Acceptance criteria clear?
   - [ ] File scope identified?
   
   If NOT ready: Report blocker and STOP.

4. Set task status:
   - Run: set_task_status --id=[task-id] --status=in-progress

5. Load minimal file context:
   - Only files relevant to this task
   - Check `/docs/architect.md` for relevant patterns
   - Check `/docs/tech-spec.md` for existing code

6. Execute task:
   - Work ONLY within task scope
   - Follow all Global Rules
   - If scope creep found → add_subtask, don't just do it

7. Verify completion:
   - [ ] All acceptance criteria met?
   - [ ] Tests pass?
   - [ ] Code follows standards?
   - [ ] No files outside scope modified?

8. Complete:
   - Run `/log` to document changes
   - Run: set_task_status --id=[task-id] --status=done
   - Run: get_next_tasks to show what's available

OUTPUT:
## ✅ Task Complete: [ID] - [Title]

### Acceptance Criteria
- [x] [Criterion 1]
- [x] [Criterion 2]

### Changes Made
| File | Change |
|------|--------|
| `path/file.ts` | [what changed] |

### Discovered Work (New Tasks)
- Task [new-id]: [title] (if any created)

### Next Available Tasks
| ID | Title | Status |
|----|-------|--------|
| [X] | [title] | Ready |
| [Y] | [title] | Blocked by [Z] |

### Notes for Other Agents
[Any handoff info needed]
```

---

## 12. `/breakdown`

**Name:** `breakdown`

**Prompt:**
```
You are breaking down a feature into TaskMaster tasks for multi-agent execution.

STEPS:
1. Read `/docs/prd.md` for feature requirements
2. Read `/docs/architect.md` for technical patterns
3. Read `/docs/tech-spec.md` for existing code/schema

4. Analyze feature complexity:
   - Run: analyze_complexity --task-id=[feature-task-id] (if exists)
   - Or manually assess scope

5. Break into atomic tasks:
   - Each task: 2-4 hours max
   - Clear file boundaries
   - Explicit acceptance criteria
   - Assigned agent type (backend/frontend/testing/docs)

6. Create tasks in TaskMaster:
   For each task:
   add_subtask --parent=[feature-id] --title="[title]" --description="[details]"
   
   Then update with full details:
   update_task --id=[new-task-id] --prompt="
   Acceptance Criteria:
   - [ ] Criterion 1
   - [ ] Criterion 2
   
   Files:
   - path/file.ts
   
   Agent: [backend/frontend/testing/docs]
   "

7. Set dependencies:
   update_task --id=[task-id] --depends-on=[dependency-ids]

8. Generate execution plan

OUTPUT:
## Feature Breakdown: [Feature Name]

### Overview
[Brief description]

### Task Dependency Graph
[T1] ──┬──► [T3] ──► [T5]
       │
[T2] ──┘──► [T4] ──┘

### Tasks Created

#### Backend Agent
| ID | Task | Est | Depends On |
|----|------|-----|------------|
| [id] | [title] | [X]h | - |
| [id] | [title] | [X]h | T1 |

#### Frontend Agent
| ID | Task | Est | Depends On |
|----|------|-----|------------|
| [id] | [title] | [X]h | T2 |
| [id] | [title] | [X]h | T2 |

#### Testing Agent
| ID | Task | Est | Depends On |
|----|------|-----|------------|
| [id] | [title] | [X]h | T3, T4 |

#### Docs Agent
| ID | Task | Est | Depends On |
|----|------|-----|------------|
| [id] | [title] | [X]h | T3 |

### Execution Strategy

**Sequential (1 agent)**: [X] hours total

**Parallel (2 agents)**:
- Agent A: T1 → T3 → T5
- Agent B: T2 → T4
- Total: ~[X] hours

**Parallel (4 agents)**:
- Backend: T1, T2
- Frontend: T3, T4 (after T2)
- Testing: T5 (after T3, T4)
- Docs: T6 (after T3)
- Total: ~[X] hours

### Ready to Start
Run `/task` to begin first available task
```

---

## 13. `/status`

**Name:** `status`

**Prompt:**
```
You are reporting project status from TaskMaster.

STEPS:
1. Get all tasks:
   - Run: get_tasks

2. Calculate progress:
   - Total tasks
   - Completed (done)
   - In progress
   - Blocked
   - Pending

3. Identify blockers:
   - Tasks marked 'blocked'
   - Tasks with unmet dependencies

4. Get next actions:
   - Run: get_next_tasks

5. Check changelog:
   - Read `/docs/changelog/pending.md`
   - Count recent changes

OUTPUT:
## 📊 Project Status

### Progress
[████████░░░░░░░░░░░░] 40% Complete

| Status | Count |
|--------|-------|
| ✅ Done | [X] |
| 🟡 In Progress | [X] |
| 🔴 Blocked | [X] |
| ⚪ Pending | [X] |
| **Total** | [X] |

### Current Blockers
| Task | Blocked By | Notes |
|------|------------|-------|
| [id]: [title] | [reason] | [notes] |

### Ready to Work
| Task | Est | Agent Type |
|------|-----|------------|
| [id]: [title] | [X]h | Backend |
| [id]: [title] | [X]h | Frontend |

### Recent Activity
- [X] changes in pending.md
- Last: [HH:MM] - [file] - [change]

### Recommended Next Steps
1. [Action 1]
2. [Action 2]
3. [Action 3]
```

---

## 14. `/setup`

**Name:** `setup`

**Prompt:**
```
You are setting up a new Anti-Gravity project with the standard structure.

STEPS:
1. Create docs folder structure:
   - Create `/docs/` folder
   - Create `/docs/changelog/` subfolder

2. Create documentation files:

   `/docs/architect.md`:
   # Architecture Overview
   
   ## Project Information
   **Project Name**: [Ask user]
   **Owner**: Vincent
   **Created**: [Today's date]
   **Last Updated**: [Today's date]
   
   ## High-Level Architecture
   [To be filled]
   
   ## Key Architectural Decisions
   [To be filled]
   
   ## File Structure
   [To be filled]
   
   ## Constraints
   [To be filled]

   `/docs/tech-spec.md`:
   # Technical Specifications
   
   ## Dependencies & Versions
   - **Node.js**: 20+
   - **Next.js**: 15.x
   - **React**: 19.x
   - **TypeScript**: 5.x
   
   ## Environment Variables
   [To be filled]
   
   ## Database Schema
   [To be filled]
   
   ## Server Actions
   [To be filled]
   
   ## Last Updated
   [Today's date]

   `/docs/prd.md`:
   # Product Requirements Document
   
   ## Project Overview
   **Project Name**: [Same as architect.md]
   **Owner**: Vincent
   **Status**: 🟡 In Development
   
   ## Problem Statement
   [To be filled]
   
   ## Features
   [To be filled]
   
   ## Success Metrics
   [To be filled]

   `/docs/webhooks.md`:
   # Webhook Documentation
   
   ## Overview
   External integration endpoints.
   
   ## Endpoints
   [To be filled as webhooks are added]

   `/docs/changelog/pending.md`:
   # Pending Changes
   
   <!-- Agent logs changes here: [HH:MM] - [FILE] - [CHANGE] - [WHY] -->

   `/docs/changelog/history.md`:
   # Project Changelog
   
   All consolidated changes recorded here.
   
   ---

3. Create `.antigravityignore`:
   # Dependencies
   package-lock.json
   pnpm-lock.yaml
   yarn.lock
   node_modules/
   
   # Build
   .next/
   dist/
   build/
   .vercel/
   
   # Environment
   .env
   .env.local
   .env.*.local
   
   # Large files
   *.log
   coverage/
   
   # OS/IDE
   .DS_Store
   .vscode/
   .idea/
   
   # Changelog (agent updates)
   docs/changelog/pending.md
   docs/changelog/history.md

4. Verify structure:
   - List all created files
   - Confirm docs folder exists

5. Initialize TaskMaster (if available):
   - Run: parse_prd (if PRD has content)

OUTPUT:
## ✅ Project Setup Complete

### Files Created
- `/docs/architect.md`
- `/docs/tech-spec.md`
- `/docs/prd.md`
- `/docs/webhooks.md`
- `/docs/changelog/pending.md`
- `/docs/changelog/history.md`
- `.antigravityignore`

### Next Steps
1. Fill in `/docs/architect.md` with architecture decisions
2. Fill in `/docs/prd.md` with feature requirements
3. Run `/breakdown` to create tasks (if using TaskMaster)
4. Run `/context` to verify setup
5. Start coding!

### Reminders
- Run `/log` after every change
- Run `/consolidate` when 5+ pending changes
- Check `/docs/` before making architectural decisions
```

---

## Quick Reference

| # | Workflow | Trigger | Purpose |
|---|----------|---------|---------|
| 1 | context | `/context` | Load session context |
| 2 | log | `/log` | Log changes |
| 3 | consolidate | `/consolidate` | Clean changelog |
| 4 | test | `/test` | Generate tests |
| 5 | fix | `/fix` | Debug bugs |
| 6 | pr | `/pr` | Prepare PR |
| 7 | docs | `/docs` | Update docs |
| 8 | perf | `/perf` | Performance audit |
| 9 | plan | `/plan` | Plan features |
| 10 | webhook | `/webhook` | Document webhooks |
| 11 | task | `/task` | Execute task |
| 12 | breakdown | `/breakdown` | Feature → tasks |
| 13 | status | `/status` | Project status |
| 14 | setup | `/setup` | New project setup |
