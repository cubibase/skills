# CubiBase Agent Skills

Agent Skills for [CubiBase](https://cubibase.com), the PostgreSQL backend platform — following the open [Agent Skills format](https://agentskills.io/specification.md).

| Skill | Description |
| --- | --- |
| [`cubibase`](cubibase/SKILL.md) | Set up and operate a CubiBase backend for the user's project: propose a schema, onboard, create tables and policies, expose the Data API, handle errors and quotas. |

## Install

**Cursor / Claude Code / any skills-compatible client** — clone into your skills directory:

```bash
# project-level (Cursor)
git clone https://github.com/cubibase/skills.git /tmp/cubibase-skills
cp -r /tmp/cubibase-skills/cubibase .cursor/skills/

# or user-level (Claude Code)
cp -r /tmp/cubibase-skills/cubibase ~/.claude/skills/
```

**Via npm** — the same skill ships inside the agent packages:

```bash
npx -y @cubibasehq/mcp   # SKILL.md at node_modules/@cubibasehq/mcp/SKILL.md
```

**Direct fetch** — no install needed:

```
https://cubibase.com/SKILL.md
```

## What the skill does

Once activated, the agent can:
1. Read the user's app and propose a database shape (tables, columns, RLS user column, exposure).
2. Guide the user through account + personal access token creation (one-time, human step).
3. Act through MCP (`@cubibasehq/mcp`), CLI (`@cubibasehq/cli`), or plain HTTPS against the OpenAPI-described Control and Data APIs.

Discovery surfaces: [llms.txt](https://cubibase.com/llms.txt) · [AI catalog](https://cubibase.com/.well-known/ai-catalog.json) · [MCP manifest](https://cubibase.com/.well-known/mcp/server.json) · [agent guide](https://cubibase.com/docs/agents).

## Validation

```bash
npx skills-ref validate ./cubibase
```

MIT © CubiBase
