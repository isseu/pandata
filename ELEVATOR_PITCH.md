# pandata elevator pitch

Repo: https://github.com/isseu/pandata
Companion files: `PROJECT.md`, `COMPETITORS.md`, `ROADMAP.md`
Status: validation only, as of 2026-11-10. No code, no prototype, no design partner, no paid pilot yet. Nothing here is a performance claim. Anything that sounds like a result is a target to prove.

Style note: short and plain, no em dashes. These lines are written to be said out loud, not read off a slide.

## The one line

Pandata is the governed query layer for agents. Boat for data access, not another agent.

## 15 seconds, any audience

Agents need to touch company data, and right now they do it with no guardrails. Pandata sits in the middle, checks every query against who is asking and what they are allowed to see, caps what it can spend, and refuses when it should not answer. It is the layer every agent has to go through.

## 30 seconds, data platform lead

You have agents querying Snowflake, Postgres, and ClickHouse, and no single layer covers all three. The warehouse natives only cover their own warehouse. Pandata is one in-path layer that parses and rewrites the SQL, enforces row and column policy for the actual user behind the agent, caps bytes and cost before execution, and caches answers safely, with the user's permissions as part of the cache key. If a question is out of scope, it refuses instead of returning a wrong number. We prove it first on your own query logs, with a 30-day paid pilot.

## 30 seconds, CISO or security lead

The risk is not the model. It is the query. An agent can ask for the wrong tables, join across systems it should not join, and return a plausible wrong number. Pandata puts policy in the query path, not in a prompt. It checks identity and per-user ACLs, masks columns, blocks banned joins, enforces a hard cost cap, and logs every decision, rewrite, cap, cache hit, and refusal. Nothing is cached across users unless the permissions cover it. That is the audit trail, not a slide about it.

## 30 seconds, founder peer or investor

Do not build another agent. Build the layer every agent must go through to touch company data. That is the Boat model: cheap, fast, invisible, measurable, embeddable. The wedge is narrow and specific: cross-engine estates where no native layer spans, plus a deterministic semantic layer with refusal. The Nov 6 report found 0 of about 25 vendors do all five data-plane functions. It also found the economics are tight, savings alone do not pay for a $12,000 a year price at base assumptions, and warehouse-native capture is the main risk. So we validate before we build: a 1-day spike on real logs, 10 design-partner interviews, then paid pilots at $500 a month. Kill if 2 or fewer will pay after 10 interviews, or the spike shows under 10 percent deflectable queries.

## 60 seconds, full version

Agents are being blocked or quietly blowing up warehouse bills for the same reason: access is unmanaged per warehouse, per user, per query.

Pandata is one governed layer in front of all of them. An agent sends a query. Pandata parses it, checks it against the business definitions and the caller's real permissions, rewrites it to stay inside those limits, estimates the cost and enforces a hard cap, and serves a safe cached answer when one exists. If it is not allowed, or not defined, it refuses.

It is built for 50 to 500 person firms that run two or more engines, Snowflake plus Postgres or ClickHouse or DuckDB, and have no platform team to self-host the open source stack. Single-warehouse shops are not the target. The native layer wins there.

The edge is not text-to-SQL accuracy. Spider 2.0 shows the same agent dropping from 91.2 percent on Spider 1.0 to 21.3 percent on real enterprise workflows, while a hand-authored semantic layer gets dbt to 100 percent in scope on a small test. The semantic layer is the accuracy. Pandata makes it enforceable and cheap, and makes refusal happen before money is spent or data leaks.

We are not building the full product yet. First a 1-day spike to measure cache hit rate and rewrite coverage on real logs, then 10 interviews, then paid pilots. If the numbers do not hold, we kill it.

## What to say when they ask

### "Is this not just an MCP gateway?"

No. Gateways do tool-level RBAC. They never parse SQL. Pandata parses SQL, rewrites it, and enforces row and column policy in the query path. That is a different layer, and a different team to build it.

### "Snowflake / Databricks already does this."

Only for their own warehouse. If you are Snowflake only, use Cortex or Genie. Pandata only earns its keep where the estate is cross-engine, and no native layer spans it. That is also the main risk: if the natives go cross-engine, the wedge closes.

### "How accurate is it?"

We do not claim an accuracy number. Accuracy comes from hand-authored business definitions in the semantic layer, not from the data plane. What the data plane does is enforce those definitions, refuse out-of-scope questions instead of guessing, and make the governed query cheap. The Nov 6 evidence for that is dbt at 100 percent in scope on 11 questions, and Cube up 17 to 23 points on 100 questions, both vendor-run and small. Treat them as direction, not proof.

### "What does it cost?"

Proposed, to test in pilots, not a list price: $500 a month under $25k a year warehouse spend, $1,000 a month for $25k to $100k, $2,500 a month above $100k. Paid pilots are $500 a month, with a 30-day savings guarantee measured on the customer's own logs. If deflection is under 10 percent, the pilot fee is refunded.

### "Who is it for first?"

A head of data platform or staff data engineer at a 50 to 500 person firm, running at least two engines in production, with an agent already touching production data or blocked by governance, who can show a warehouse bill or query history. Not a single-warehouse shop, not a firm with no agent usage, not a consultant.

### "Why now?"

Two reasons. First, agents are moving into production data faster than governance is, and Gartner expects over 40 percent of agentic projects to be cancelled by end 2027 on cost, unclear value, and inadequate risk controls. Second, in Chile, Law 21.719 is in full force from 1 Dec 2026 and Open Finance (NCG 514) from July 2027, so consent and audit evidence have a deadline. Neither reason proves willingness to pay. That is what the interviews are for.

### "What is the competition?"

Closest partials are Dremio, now SAP, and Immuta. Neither does ACL-aware semantic caching with per-user keying, or cost caps, as a documented product feature. The crowded side is generic MCP gateways: Runlayer, Arcade, ContextForge, and the warehouse natives. Credible Data raised a $10M seed in July 2026 for a governed query engine, and CData and Celigo added MCP governance in Oct 2026, so the governed-query space is getting crowded too. See `COMPETITORS.md` for the full map.

## What not to say

- Do not claim a cache hit rate, a cost saving, a latency number, or an accuracy score. All of those are to prove in the spike and pilots.
- Do not claim Spider 2.0 leaderboard scores as product evidence. The Jan 2026 audit found annotation errors in 62.8 percent of Spider 2.0-Snow items.
- Do not claim the data plane fixes accuracy. It does not. The semantic layer does, and authoring it per customer is the real cost.
- Do not pitch it as a company yet. The Nov 9 review scored it 2/5 as a standalone company build. It is a validation sprint with a kill line.
- Do not pitch single-warehouse firms. That is the natives' market, and we lose there.

## The ask, by audience

- Data platform lead: 30 minutes, and a query-history export or warehouse bill to run the spike against. No install, no production access.
- CISO: 30 minutes, to walk through the policy model, the cache key, and the audit log. No data leaves your boundary in the spike.
- Design partner: 10 interviews, then a $500 a month paid pilot if the spike holds, measured on your own logs.
- OEM target (ClickHouse, MotherDuck, Cube, dbt Labs, Starburst): a conversation after the spike and interviews, about an embeddable layer, not a sales pitch now.
- Founder peer: a read of `PROJECT.md` and `COMPETITORS.md`, and a hard question about the kill line.

## One-page version, to paste into an email

Subject: pandata, the governed query layer for agents

Agents need to touch company data, and right now they do it with no guardrails.

Pandata is one layer every agent has to go through. It parses and rewrites the SQL, enforces row and column policy for the actual user behind the agent, caps bytes and cost before execution, caches answers safely with permissions as part of the cache key, and refuses when a question is not allowed or not defined. It logs every decision.

It is for 50 to 500 person firms running two or more engines, where no native layer spans them. It is not another agent, not a generic MCP gateway, and not a warehouse-native tool.

We are validating before building: a 1-day spike on real query logs, 10 design-partner interviews, then $500 a month paid pilots. Kill if 2 or fewer will pay after 10 interviews, or the spike shows under 10 percent deflectable queries.

If you run a cross-engine estate and an agent already touches it, or is blocked from doing so, I would like 30 minutes and a query-history export to test it against.
