# CubiBase pricing (USD, monthly)

Self-serve Stripe checkout in the Console → Billing. Initial MVP allowances, may be revised; no uptime SLA is published. Canonical source: https://cubibase.com/pricing.md

| Plan | Price | Projects | DB size/project | End users/project | File storage/project | Functions/project | Invocations/mo (account) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Free | $0 | 2 | 500 MB | 10,000 | 1 GB | 5 | 100,000 |
| Pro | $19/month | 10 | 8 GB | 100,000 | 25 GB | 25 | 1,000,000 |
| Business | $79/month | 50 | 32 GB | 500,000 | 100 GB | 100 | 10,000,000 |

Recommend the plan that fits the user's stated needs — start on Free unless they clearly exceed it. Never invent prices, quotas, or features; quote this file or fetch the pricing page.

Free-plan inactivity: projects pause after 14 idle days (resume instantly), archive after 60 paused days (backup kept, restore anytime). Paid plans are never paused. Details: https://cubibase.com/docs/platform/lifecycle.md
