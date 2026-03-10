
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What is Paperclip

Paperclip is an open-source Node.js server + React UI that orchestrates AI agent teams to run autonomous businesses. It provides org charts, budgets, governance, goal alignment, heartbeat scheduling, and agent coordination. Think "company OS for AI agents" — not a chatbot or workflow builder.

## Build & Dev Commands

```bash
pnpm install              # Install all workspace dependencies
pnpm dev                  # Full dev (API + UI, watch mode) — serves at http://localhost:3100
pnpm dev:once             # Full dev without file watching
pnpm dev:server           # Server only
pnpm build                # Build all packages
pnpm typecheck            # Type-check all packages
pnpm test                 # Run tests in watch mode (vitest)
pnpm test:run             # Run tests once
vitest run server         # Run tests for a single project (server, ui, cli, packages/db, etc.)
pnpm db:generate          # Generate Drizzle DB migration
pnpm db:migrate           # Apply migrations
pnpm paperclipai <cmd>    # Run the CLI (e.g. `pnpm paperclipai doctor --fix`)
```

## Monorepo Structure

pnpm workspace with these packages:

| Package | Path | Purpose |
|---------|------|---------|
| `@paperclipai/server` | `server/` | Express 5 API server, embedded Postgres, heartbeat scheduler, auth (better-auth), WebSocket realtime |
| `@paperclipai/ui` | `ui/` | React 19 + Vite + Tailwind CSS 4 + Radix UI + TanStack Query SPA |
| `@paperclipai/db` | `packages/db/` | Drizzle ORM schema, migrations, embedded-postgres client |
| `@paperclipai/shared` | `packages/shared/` | Shared types, enums, Zod schemas used by all packages |
| `@paperclipai/adapter-utils` | `packages/adapter-utils/` | Common adapter utilities |
| `paperclipai` (CLI) | `cli/` | CLI tool (commander) — onboard, doctor, configure, issue management |
| Adapters | `packages/adapters/*/` | Agent adapters: `claude-local`, `codex-local`, `cursor-local`, `openclaw-gateway`, `opencode-local`, `pi-local` |

## Architecture

**Server** (`server/src/`):
- `routes/` — Express route handlers (companies, agents, issues, goals, projects, approvals, costs, secrets, etc.)
- `services/` — Business logic layer (heartbeat scheduling, issue approvals, live events, agent permissions, company portability)
- `adapters/` — Agent runtime adapters with a registry pattern (`registry.ts`). Adapters bridge between Paperclip and external agent runtimes (Claude Code, Codex, Cursor, OpenClaw, etc.)
- `auth/` — Authentication via better-auth
- `middleware/` — Express middleware (authz, etc.)
- `realtime/` — WebSocket-based live event broadcasting
- `secrets/` — Secret storage with local encryption and strict mode
- `config.ts` — Central config loading from env vars + config file (`~/.paperclip/instances/default/config.json`)

**Database** (`packages/db/src/schema/`):
- Drizzle ORM with PostgreSQL. ~35 schema files covering companies, agents, issues, goals, projects, approvals, costs, heartbeats, invites, labels, secrets, etc.
- Embedded PostgreSQL auto-starts in dev (no external DB setup needed). Data at `~/.paperclip/instances/default/db`

**UI** (`ui/src/`):
- React Router DOM for routing, TanStack Query for data fetching
- `pages/` — Page components
- `components/` — UI components (Radix-based)
- `api/` — API client layer
- `hooks/` — React hooks
- `adapters/` — Client-side adapter UI (config forms, etc.)

**Key domain concepts**: Companies → Projects → Issues (tickets), Agents with heartbeats, Goals with ancestry, Org chart hierarchy, Budget/cost tracking, Approval gates, Skills (runtime-injectable), Invites for agent onboarding.

## Testing

Vitest with workspace projects configured in `vitest.config.ts`. Test projects: `packages/db`, `packages/adapters/opencode-local`, `server`, `ui`, `cli`. Server tests are in `server/src/__tests__/`.

## Lockfile Policy

Do NOT commit `pnpm-lock.yaml` in PRs. CI on master regenerates it automatically.

## Deployment Modes

Two modes controlled by `PAPERCLIP_DEPLOYMENT_MODE`:
- `local_trusted` (default) — No auth, company deletion enabled, binds to 127.0.0.1
- `authenticated` — Auth enabled via better-auth, requires `BETTER_AUTH_SECRET`, private/public exposure

## Key Environment Variables

- `DATABASE_URL` — External Postgres (unset = embedded Postgres)
- `PAPERCLIP_DEPLOYMENT_MODE` — `local_trusted` or `authenticated`
- `PAPERCLIP_HOME` / `PAPERCLIP_INSTANCE_ID` — Override default paths (`~/.paperclip/instances/default/`)
- `PORT` — Server port (default 3100)
