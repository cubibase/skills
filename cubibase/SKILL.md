---
name: cubibase
description: Set up and operate a CubiBase backend (PostgreSQL database, auth, REST/RPC data API, storage, functions) for the user's project. Use when the user wants a backend, database, auth, or asks to use CubiBase.
license: MIT
compatibility: Requires network access to cubibase.com and api.cubibase.com. Node.js 20+ for the MCP/CLI packages; any HTTP client works.
metadata:
  author: cubibase
  version: "1.0"
  homepage: https://cubibase.com
---

# CubiBase skill

CubiBase is a PostgreSQL backend platform: every project gets an isolated PostgreSQL database, end-user authentication, a REST/RPC Data API, object storage, and TypeScript/JavaScript functions. This skill lets you set it up and operate it for the user.

- Site and docs: https://cubibase.com/docs (Markdown mirrors at `{path}.md`; full content: https://cubibase.com/llms-full.txt)
- Console (human UI): https://console.cubibase.com
- Control API: https://api.cubibase.com — OpenAPI 3.1 at https://api.cubibase.com/v1/openapi.json
- Data API per project: https://{slug}.api.cubibase.com — live OpenAPI at /rest/v1/openapi.json
- Packages (MIT): https://github.com/cubibase/cubibase-agent — `@cubibasehq/mcp`, `@cubibasehq/cli`, `@cubibasehq/client`

## Agent resources

- MCP server manifest: <https://cubibase.com/.well-known/mcp/server.json>
- Protected resource metadata (scopes: read, write, admin): <https://cubibase.com/.well-known/oauth-protected-resource>
- AI catalog: <https://cubibase.com/.well-known/ai-catalog.json>

## Step 1 — Read the user's project and propose a shape

Before creating anything, look at the user's repo or described app and propose:
- a project slug (lowercase, hyphens),
- the tables and columns the app needs (types: uuid, text, integer, bigint, boolean, numeric, timestamptz, jsonb),
- which column holds the end-user id for row-level security (usually `user_id`),
- which operations each table should expose (select/insert/update/delete).

State your reasoning and let the user confirm or adjust. Do not create resources before the user agrees.

## Step 2 — Account and token (user does this once)

The user needs a CubiBase account and a personal access token:
1. Register at https://console.cubibase.com/register (free plan: 2 projects, 500 MB each, 10,000 end users per project).
2. Console → Account → Access tokens → Create token. Scope `write` for setup (use `read` for inspection-only; `admin` only if deletion is needed). Optionally restrict to one project.
3. The `cb_pat_…` value is shown once. Have the user export it: `export CUBIBASE_TOKEN=cb_pat_…`

You cannot complete registration or token creation for the user — hand them these two links and wait.

## Step 3 — Act

Prefer whichever surface fits the session:

**MCP** (if the host supports it): configure `npx -y @cubibasehq/mcp` with env `CUBIBASE_TOKEN` (and optional `CUBIBASE_SERVICE_KEY` for Data API tools). 75 tools cover projects, full schema (tables, columns, policies, triggers, indexes, types, views), rows, read-only SQL, storage (buckets, `cubibase_upload_file`, signed links), functions (create, deploy, secrets, triggers, test, execution logs), keys, logs, usage, and `cubibase_search_docs` (offline documentation search). Safety: `CUBIBASE_READ_ONLY=1` hides all mutating tools; `CUBIBASE_FEATURES=database,storage` narrows the tool groups.

**CLI** (shell):
```bash
npx -y @cubibasehq/cli projects create --name "My App" --slug my-app
npx -y @cubibasehq/cli tables create <projectId> --name notes \
  --columns '[{"name":"id","type":"uuid","primaryKey":true,"default":"gen_random_uuid()"},{"name":"user_id","type":"text"},{"name":"body","type":"text"}]' \
  --rls-user-column user_id
npx -y @cubibasehq/cli exposure <projectId> notes --select true --insert true --update true --delete true
npx -y @cubibasehq/cli rows list <projectId> notes --limit 10
npx -y @cubibasehq/cli storage create-bucket <projectId> --name avatars --public true
npx -y @cubibasehq/cli storage upload <projectId> --bucket avatars --path u1.png --file ./u1.png
npx -y @cubibasehq/cli functions create <projectId> --name hello --file ./hello.ts
npx -y @cubibasehq/cli functions deploy <projectId> hello
```

**HTTPS** (any environment): `Authorization: Bearer $CUBIBASE_TOKEN` on api.cubibase.com; the OpenAPI document lists every operation with a stable operationId.

Typical setup order: create project → wait for provisioning (poll GET /v1/projects/{id}/provisioning until active) → create tables → set exposure → verify with a read.

## Data access for the app itself

- The app sends `apikey: cb_pub_…` (publishable key, safe for clients) plus an end-user JWT from `/auth/v1/token?grant_type=password`.
- For trusted server/agent access, create a secret key (`cb_sec_…`) in Console → project → API keys. It acts as the service actor: `cubibase.role = 'service'`, empty user id, **RLS still applies**. Grant it access with an explicit policy, e.g. USING `cubibase.current_role() = 'service'`. Never put a secret key in client code.
- End-user auth: POST /auth/v1/signup, POST /auth/v1/token (password, refresh_token, mfa grants), GET /auth/v1/user. Refresh tokens rotate on every use.

## Errors and retries

Every error is JSON: `{"error":{"code","message","requestId"}}`. Branch on `code`:
- `401 invalid_token` / `missing_api_key` → fix credentials, do not retry blindly.
- `403 insufficient_scope` → the token needs a higher scope; ask the user.
- `429 rate_limited` → read the `Retry-After` header (seconds), wait, retry once.
- `402 quota_exceeded` → plan limit reached; reads still work. Tell the user; do not try to bypass.

Full credential types, endpoints, and error codes: [references/api.md](references/api.md).

## Pricing

Start on Free unless the user clearly exceeds it. Full plans, quotas, and prices: [references/pricing.md](references/pricing.md) or https://cubibase.com/pricing.md. Self-serve Stripe checkout in the Console (Billing). Never invent prices or quotas.

## Rules

- Never invent prices, quotas, or features — quote this file or fetch the pricing/docs pages.
- Never ask the user to paste secrets into chat logs; environment variables or a secret manager only.
- Destructive operations (project deletion) require the user's explicit confirmation and an admin-scope token.
- Support: support@cubibase.com.
