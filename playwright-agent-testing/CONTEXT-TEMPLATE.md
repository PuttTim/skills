# Context File Template

When `.context/playwright-context.md` does NOT exist, generate it by scanning the codebase once. This file caches codebase-specific details so future runs skip discovery.

## Template

```markdown
# Playwright Testing Context

## App URLs
- Frontend: {frontend URL, e.g. http://localhost:3000}
- API: {backend URL, e.g. http://localhost:8080}

## Login Flow
- Login page: {path}
- Fields: {describe form fields and submit button by accessible name}

## Impersonation (if applicable)
- {describe how to switch between user accounts for role-gate testing}

## Database Access
- {how to connect: CLI command, container exec, connection string, etc.}
- {note any ORM conventions for column/table name casing}

## Test Accounts
| Name | Email | Role | Notes |
|------|-------|------|-------|
| ... | ... | ... | ... |

## Key Tables
| Table | Key Columns | Notes |
|-------|-------------|-------|
| ... | ... | ... |

## Navigation Structure
- {describe the app's main navigation and how to reach key pages}
- {note URL patterns if sections are defined by query params or route segments}

## Known Quirks
- {anything discovered during testing: toast timing, URL params not restoring state, etc.}
```

## How to populate

1. **App URLs:** Check project config (`docker-compose.yml`, `.env`, `Makefile`, `package.json` scripts)
2. **Login flow:** Open the browser, navigate to the app, take a snapshot of the login page
3. **DB access:** Check how the database runs (Docker container, local service, cloud) and find the CLI command
4. **Test accounts:** Check seed data, fixtures, or environment files for pre-configured users
5. **Key tables:** Check migration files, ORM models, or run a table-listing command
6. **Navigation:** Navigate the app and take snapshots to map out the sidebar/menu structure

Keep the file updated if you discover new info during testing.
