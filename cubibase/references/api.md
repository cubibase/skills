# CubiBase API reference (quick)

Canonical, always-current sources:
- Control API OpenAPI 3.1: https://api.cubibase.com/v1/openapi.json (operationIds, typed errors, rate limits)
- Per-project Data API OpenAPI: https://{slug}.api.cubibase.com/rest/v1/openapi.json (requires the project `apikey` header)
- Docs as markdown: https://cubibase.com/docs/{page}.md · everything: https://cubibase.com/llms-full.txt

## Authentication surfaces

| Credential | Prefix | Where | Acts as |
| --- | --- | --- | --- |
| Personal access token | `cb_pat_` | Console → Account → Access tokens | The developer (Control API). Scopes: read < write < admin; optional project restriction and expiry |
| Publishable key | `cb_pub_` | Console → project → API keys | Anonymous end-user access (Data API); RLS enforced; safe in client code |
| Secret key | `cb_sec_` | Console → project → API keys | Service actor (`cubibase.role = 'service'`); RLS still applies; server-side only |
| End-user JWT | eyJ… | POST /auth/v1/token | The signed-in end user; RLS uses the JWT subject |

## Control API (api.cubibase.com)

`Authorization: Bearer $CUBIBASE_TOKEN`. Key operations (see OpenAPI for all):
- Projects: listProjects, createProject, getProject, updateProject, getProvisioning, deleteProject
- Schema: listTables, createTable, addColumn, dropColumn, setExposure, listPolicies, createPolicy, dropPolicy, createSqlFunction, dropSqlFunction
- Database objects: db-triggers (list/create/enable/drop), indexes (list/create/drop), types (enum/composite/labels), views (list/create/refresh/api-access/drop)
- Data tools: listRows, insertRow, updateRow, deleteRow, runSql (read-only)
- Storage: buckets CRUD, objects list/delete, uploads (presigned S3 POST + complete), sign (public/signed download URL), usage
- Functions: CRUD, deployments (create/list/activate), secrets (set/delete), triggers (http/database/storage/schedule), test invocation, executions, templates, usage
- Observability: requestLogs, auditLogs, usage, api-keys (list/create/revoke)
- Billing (read-only): catalog (public pricing incl. additionalUsage rates), overage/settings, overage/usage, overage/invoice-preview. Activation and spending-limit changes are owner-only financial consent actions in the Console (Billing → Additional Usage), not agent operations
- Tokens: listTokens, createToken, revokeToken

All of the above are wrapped by `@cubibasehq/client` (typed methods), `@cubibasehq/mcp` (76 tools; `CUBIBASE_READ_ONLY=1`, `CUBIBASE_FEATURES` groups), and `@cubibasehq/cli` (`storage`, `functions`, `triggers`, `indexes`, `types`, `views`, `policies` commands).

## Data API ({slug}.api.cubibase.com)

Headers: `apikey: cb_pub_…` (or `cb_sec_…`) + `Authorization: Bearer <user JWT>` when acting as a user.
- REST: GET/POST/PATCH/DELETE `/rest/v1/{table}` — filters `col=eq.value`, `order=col.asc`, `limit`, `offset`, `select`
- RPC: POST `/rest/v1/rpc/{function}`
- Auth: POST `/auth/v1/signup`, POST `/auth/v1/token?grant_type=password|refresh_token|mfa`, GET `/auth/v1/user`
- Storage: `/storage/v1/…` buckets, uploads, signed links
- Functions: `/functions/{name}`

## Error envelope

Every error: `{"error":{"code","message","requestId"}}` with HTTP status.
- 401 invalid_token / missing_api_key → fix credentials
- 403 insufficient_scope → higher scope needed (ask the user)
- 403 project_paused → Free plan inactivity; resume in Console
- 429 rate_limited → wait `Retry-After` seconds, retry once
- 402 quota_exceeded → plan limit; reads still work; tell the user (paid orgs may opt in to metered additional usage in the Console — only with explicit user instruction)
- 402 spending_limit_reached → org enabled additional usage and hit its monthly spending limit; included quotas unaffected; the owner can raise the limit, reduce usage, or wait for reset
