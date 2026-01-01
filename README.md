# Anti-Gravity Setup

Standard setup for Google Anti-Gravity projects with TaskMaster integration.

## Quick Start (New Project)

### Option A: Use `/setup` Workflow (Recommended)
1. Create project in Anti-Gravity
2. Paste Global Rules (see `global-rules.md`)
3. Create `/setup` workflow (see `workflows.md`)
4. Run `/setup` in Anti-Gravity
5. Create remaining workflows from `workflows.md`
6. Customize `workspace-rules.md` and paste

### Option B: Manual Setup
1. Copy `docs-templates/` folder to your project as `docs/`
2. Copy `.antigravityignore` to project root
3. Paste `global-rules.md` into Anti-Gravity Global Rules
4. Customize and paste `workspace-rules.md`
5. Create all 14 workflows from `workflows.md`

## Files

| File | Purpose |
|------|---------|
| `global-rules.md` | Paste into Customizations → Rules → Global |
| `workspace-rules.md` | Template for project-specific rules |
| `workflows.md` | All 14 workflows to create |
| `docs-templates/` | Documentation file templates |
| `.antigravityignore` | Files to exclude from Anti-Gravity |

## Workflows

| Workflow | Purpose |
|----------|---------|
| `/setup` | Initialize new project structure |
| `/context` | Load session context |
| `/log` | Log code changes |
| `/consolidate` | Clean up changelog |
| `/test` | Generate tests |
| `/fix` | Debug systematically |
| `/pr` | Prepare pull request |
| `/docs` | Update documentation |
| `/perf` | Performance audit |
| `/plan` | Plan complex features |
| `/webhook` | Document webhooks |
| `/task` | Execute TaskMaster task |
| `/breakdown` | Break feature into tasks |
| `/status` | Project status report |

## Daily Workflow

```
1. /context      → Load project context
2. /status       → Check progress (if using TaskMaster)
3. /task         → Work on next task
4. /log          → Document changes
5. Repeat 3-4
6. /consolidate  → If 5+ pending changes
```

## MCP Servers

This setup assumes these MCPs are installed:
- Vercel
- Notion
- GitHub
- Supabase
- Context7
- Sequential Thinking
- Perplexity Ask
- TaskMaster AI

## Maintenance

- Update `global-rules.md` when patterns change
- Add new workflows to `workflows.md` as needed
- Keep `docs-templates/` current with best practices
