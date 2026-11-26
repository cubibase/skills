# CubiBase pricing (USD, monthly) — catalog v4.5

Self-serve Stripe checkout in the Console → Billing. No uptime SLA is published. Canonical source: https://cubibase.com/pricing.md

Every plan includes one project. Add more projects for $9/month each on Pro and Business. Upgrade your plan without changing your project count: switching between Pro and Business preserves purchased project slots and their charges.

Database storage is allocated per project. All other consumption quotas are shared across the organization. Additional projects add project slots only — shared quotas stay unchanged.

Total bandwidth covers API responses and file delivery combined; API bandwidth is a sublimit of the total, not an extra allowance. Exhausting the API sublimit never blocks remaining file bandwidth. Bandwidth counts outbound bytes delivered to customers (binary units: 1 GB = 1024 MB).

| Feature | Free | Pro | Business |
| --- | --- | --- | --- |
| Monthly price | $0 | $25/month | $99/month |
| Projects | 1 | 1 included + $9/mo each additional | 1 included + $9/mo each additional |
| Database per project | 500 MB | 10 GB | 10 GB |
| Object storage (shared) | 2 GB | 100 GB | 250 GB |
| Total bandwidth/mo (shared; API + file delivery) | 15 GB | 500 GB | 750 GB |
| API bandwidth sublimit/mo (shared) | 5 GB | 25 GB | 100 GB |
| Monthly active users (shared) | 50,000 | 100,000 | 500,000 |
| API requests/mo (shared) | 500,000 | 1,000,000 | 5,000,000 |
| Storage write operations/mo (shared) | 10,000 | 100,000 | 300,000 |
| File download requests/mo (shared) | 100,000 | 250,000 | 2,000,000 |
| Combined CDN requests/mo (shared) | 1,000,000 | 1,500,000 | 7,000,000 |
| Function invocations/mo (shared) | 500,000 | 2,000,000 | 6,000,000 |
| Function compute GB-seconds/mo (shared) | 10,000 | 50,000 | 300,000 |
| Functions per project | 5 | 25 | 100 |
| Function timeout | 5 sec | 30 sec | 60 sec |
| Function memory | 128 MB | 256 MB | 512 MB |
| Maximum file size | 50 MB | 1 GB | 5 GB |
| Daily backup retention | None | 7 days | 14 days |
| Inactivity pause | After 14 idle days | Never | Never |

All plans include Database, Authentication, Storage and Functions, spending protection, MCP, CLI and Agent Skills. By default, quotas refuse operations at the limit — nothing is auto-billed. Paid plans can explicitly opt in to metered additional usage with a spending limit (below).

Additional projects (paid plans only): $9/month each, with their own 10 GB database. The subscription quantity follows the actual billable project count: creating a project adds a prorated charge (Stripe invoice preview shown in the Console before confirmation); deleting one reduces the quantity with a prorated credit on the next invoice. Additional projects are a fixed subscription item, never metered usage. Example: Pro with five projects = $25 + 4 × $9 = $61/month.


## Additional usage (V4.5, optional metered overages)

Disabled by default; Pro and Business only (Free is never eligible). An organization owner or billing admin enables it in Console → Billing → Additional Usage after accepting the exact rates and setting a monthly spending limit ($5, $10, $25, $50, $100, custom, or no limit with explicit confirmation). Until enabled, quotas refuse operations at the limit — nothing is auto-billed. When enabled, excess consumption bills at these rates under the spending limit; reaching the limit returns 402 spending_limit_reached (included quotas, billing access and deletion keep working; data is never deleted).

| Resource | Billing unit | Price USD |
| --- | --- | --- |
| API Bandwidth (preview rate) | GB | $0.09 |
| API Requests (preview rate) | 1 million requests | $3 |
| Authentication (preview rate) | 1,000 additional MAU | $0.50 |
| CDN Requests (preview rate) | 1 million requests | $4 |
| Database | GB-month | $0.20 |
| File Bandwidth (preview rate) | GB | $0.03 |
| Function Compute (preview rate) | 100,000 GB-seconds | $3 |
| Function Invocations (preview rate) | 1 million invocations | $1 |
| Storage | GB-month | $0.03 |

Storage rates are approved; rates marked "preview rate" are implementation rates validated against infrastructure costs before general availability — always quote them as preview pricing. Storage overages are time-weighted excess GB-months (database excess measured per project; file storage shared per organization); bandwidth uses decimal GB (1 GB = 1,000 MB) for the excess only. API bandwidth is part of total bandwidth — no byte is billed twice. API and CDN request quotas are independent: one request through CloudFront can consume both. Alerts at 50/75/90/100% of the limit; metering delays mean a small bounded overshoot is possible (not an absolute invoice cap). Disabling stops future metered charges but never cancels usage already validly accrued.

Recommend the plan that fits the user's stated needs — start on Free unless they clearly exceed it. Never invent prices, quotas, or features; quote this file or fetch the pricing page.

Free-plan inactivity: projects pause after 14 idle days (resume instantly), archive after 60 paused days (backup kept, restore anytime). Paid plans are never paused. Details: https://cubibase.com/docs/platform/lifecycle.md
