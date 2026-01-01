# Technical Specifications

**Last Updated**: [Date]

---

## Dependencies & Versions

### Core
- **Node.js**: 20+
- **Next.js**: 15.x
- **React**: 19.x
- **TypeScript**: 5.x (strict)

### Styling
- **Tailwind CSS**: 4.x
- **shadcn/ui**: Latest

### Database & Auth
- **Supabase**: Latest
- **@supabase/ssr**: Latest

### Validation
- **Zod**: Latest

### Testing
- **Vitest**: Latest
- **@testing-library/react**: Latest

---

## Environment Variables

```bash
# Supabase
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=  # Server only

# App
NEXT_PUBLIC_APP_URL=http://localhost:3000
```

---

## Database Schema

### Table: users
```sql
id              UUID        PRIMARY KEY
email           TEXT        UNIQUE NOT NULL
full_name       TEXT
created_at      TIMESTAMPTZ DEFAULT NOW()
updated_at      TIMESTAMPTZ DEFAULT NOW()
```

[Add more tables as built]

---

## Server Actions

### Authentication
| Action | Parameters | Returns |
|--------|------------|---------|
| `signUpAction` | email, password | `ActionResponse<User>` |
| `signInAction` | email, password | `ActionResponse<User>` |
| `signOutAction` | - | `ActionResponse<null>` |

[Add more as built]

---

## Useful Commands

```bash
npm run dev       # Development
npm run build     # Production build
npm run test      # Run tests
npm run lint      # Check code
```
