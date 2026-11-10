# pandata

Repo: https://github.com/isseu/pandata

Status: validation only, as of 2026-11-10. The repo was created empty. No code, no prototype, no design partner, no paid pilot yet. Nothing in this file is a performance claim. Anything that sounds like a result is a target to prove, not a fact.

## One line

The governed query layer for agents. Boat for data access, not another agent.

Every agent that touches company data has to go through pandata first. Pandata decides what that agent may ask, rewrites the query so it stays inside those limits, caps what it can spend, serves a safe cached answer when one exists, and logs everything. If a question is not allowed, or not defined, it refuses instead of guessing.

## The problem

Agents are blocked, feared, or quietly expensive for the same three reasons:

1. Access is unmanaged per warehouse, per user, per query. An agent can ask for the wrong tables, the wrong columns, or a full scan nobody approved.
2. Cost is unmanaged. One bad agent query can scan terabytes, and agent loops repeat near-identical queries all day.
3. Numbers are unmanaged. Text-to-SQL will cheerfully return a wrong number when the business definition is missing. A wrong number is worse than no number.

The teams that feel this first are data platform teams in firms where agents already touch production data, or are blocked from doing so by governance rather than by lack of interest.

## What pandata does

Five data-plane functions, in path, before a query reaches a warehouse:

1. Parse and rewrite SQL. Parse agent SQL, check it against the semantic layer and policy, rewrite it to aggregation-only where required, block banned joins and cross-warehouse joins, and deny columns and tables the caller may not see.
2. Enforce row and column policy per user. Policy follows the human user behind the agent, not just the agent identity. Apply row filters, column masking, and cell-level rules, and refuse when the effective permission does not cover the request.
3. Cap bytes, rows, and cost. Estimate cost before execution, enforce a hard cap per query and per caller, and refuse or rewrite before money is spent.
4. Cache and dedup, ACL-aware. Normalise SQL to a canonical form, then serve a semantically equivalent cached result only when the cached answer was computed under permissions that cover the current caller. Coalesce duplicate in-flight queries. A cache that ignores permissions is a leak, so permissions are part of the cache key.
5. Log everything and refuse cleanly. Every decision, rewrite, cap, cache hit, and refusal is logged with the user, the tables and columns touched, and the cost estimate. Refusal is a first-class outcome, not an error to work around.

On top of the data plane sits a deterministic semantic layer: hand-authored business definitions and metrics, compiled to SQL, with refusal for out-of-scope questions. The data plane makes that layer enforceable. The layer alone does not create the definitions, and pandata does not claim otherwise.

## Flow

```
agent
  -> pandata (identity + per-user ACL)
  -> parse SQL, check semantic layer
  -> rewrite / mask / filter, or refuse
  -> estimate cost, check caps
  -> ACL-aware cache lookup
  -> execute on Snowflake / BigQuery / Databricks / Postgres / ClickHouse / DuckDB
  -> log decision, query, cost, and result
  -> answer, or refusal with a reason
```

## Who it is for first

Target: 50 to 500 person firms that run two or more of Snowflake, BigQuery, Databricks, Postgres, ClickHouse, or DuckDB/MotherDuck in production, plus SaaS-backed tables agents query, and have no single native layer that spans them. They have a data platform owner who owns the warehouse bill, and no platform team that would self-host the open source stack.

Screen in only if all three hold:

- At least two engines in production, or a warehouse plus at least one SaaS-backed table agents query.
- An agent or natural-language tool already touches production data, or is blocked from doing so by governance.
- They can show a warehouse bill or query-history export, not just describe one.

Screen out: single-warehouse shops (the native layer will win there), firms with no agent usage, firms under 50 or over 500 people, consultants and vendors.

A secondary beachhead is regulated Chile and LatAm financials, where consent and audit evidence are the product as much as cost: 19 banks plus roughly 40 authorised fintechs in Chile, with Law 21.719 in full force from 1 Dec 2026 and Open Finance (NCG 514) in force July 2027. That is a validation market, not a venture-scale TAM on its own.

## What pandata is not

- Not another agent, chatbot, or text-to-SQL product. It does not generate the question. It governs the query.
- Not a generic MCP gateway. Gateways do tool-level RBAC. Pandata parses SQL.
- Not a context factory or semantic layer alone. It does not claim that a data plane fixes accuracy. Accuracy comes from hand-authored business definitions; pandata makes them enforceable and cheap.
- Not a warehouse-native agent layer. Snowflake Cortex, Databricks Genie, BigQuery, and ClickHouse Agents are single-warehouse by design. Pandata only earns its keep where the estate is cross-engine.
- Not a BI tool. It has no dashboard and no chart. It is infrastructure underneath agents and tools.

## Boat principles

Borrowed from Boat (boat.dev): the model, not the product.

- Invisible. Agents and analysts should not have to think about it.
- Measurable. The headline has to be a number on the customer's own bill: cheaper governed queries, hard caps held, zero leaked rows. To prove, not yet proven.
- Full, not a toy. It has to handle the real estate, real permissions, and real query logs, or it is a demo.
- Embeddable / OEM. The layer should be usable inside another vendor's product as well as direct. Candidate OEM targets, uncontacted: ClickHouse, MotherDuck, Starburst, Cube, dbt Labs. Snowflake and Databricks are capture risk, not a channel.

## Why this might be real

From the Nov 6, 2026 quantitative market-gap report (see `COMPETITORS.md` for the full map):

- 0 of about 25 vendors reviewed do all five data-plane functions across warehouses and Postgres / ClickHouse / DuckDB. The closest partials are Dremio and Immuta, each doing three or more only partially.
- No vendor documents ACL-aware semantic result caching with per-user keying as a product feature. Every cache found is either the warehouse's own exact-text result cache, broken by whitespace or predicate order in agent SQL, or a BI pre-aggregation.
- The accuracy evidence points the same way. Spider 2.0 raw accuracy collapses from 91.2% on Spider 1.0 to 21.3% for the same agent, while a hand-authored semantic layer gets dbt to 100% in scope on a small test (n=11) and Cube to plus 17 to 23 points (n=100). The semantic layer is the accuracy. The data plane is what enforces it.

## Why this might not work

Honest limits, from the same Nov 6 report. The provisional score was 29/45, below the 31/45 pass bar. Proceed only as a 2 to 4 week validation sprint, not as a funded build.

- Savings alone do not pay for it at base assumptions. A base firm at $24k/yr warehouse spend, 20% cache deflection, saves about $5,800/yr in warehouse plus LLM cost. That is less than a $12,000/yr price. It needs a higher spend firm, a real deflection rate near 49%, or a compliance and audit value priced separately.
- The buyer is unclear. The data platform owner owns the warehouse bill, the CISO owns governance, the AI platform owner owns agents. Gateway price anchors are $29 to $125/mo. A $1,000/mo price can only be justified as a warehouse-cost product, with the saving measured on the customer's own bill.
- Warehouse-native capture is the highest risk. Snowflake, Databricks, and ClickHouse all ship native agent layers, Genie was free for named users until 31 Jan 2027, and SAP bought Dremio in July 2026 for federated plus semantic plus MCP. Pandata survives only where the estate is cross-engine.
- The semantic layer is an authoring problem, not a proxy problem. Hand-authored definitions per customer are the real cost. A Rust data plane does not create them.
- No network effect and no data moat. Semantic definitions belong to the customer. Dremio or Immuta could add a cache or cap feature in one or two quarters. The moat, if any, is execution and trust.

## Validation plan, in order

1. One-day technical spike, before any interviews. Measure on real query logs, not a demo:
   - Normalised cache hit rate on 1,000 real consecutive queries, exact-text vs normalised/semantic hits.
   - SQL rewrite coverage on 50 agent-style queries: percent that parse, are aggregation-only, contain a blocked join, or touch a denied column.
   - Latency overhead, p50 and p95, target under 5ms p50 and under 20ms p95.
   - Cap enforcement: bytes and rows estimated pre-execution vs actual, percent that would have exceeded a $0.50 cap.
   - Kill bar: normalised hit rate under 10% and rewrite coverage under 70%. Pass bar: hit rate at or above 20% and p95 overhead under 20ms.
2. Ten design-partner interviews, weeks 2 to 3. Five Chile/LatAm financials, five US/EU mid-market SaaS or fintech, screened as above. Exact questions are in the Nov 6 report, section 6.
3. Paid pilots at $500/mo, only if at least three partners commit on the basis of their own bill, with a 30-day savings guarantee measured on their own query logs. If deflection is under 10%, the pilot fee is refunded.
4. OEM outreach, only after the spike and interviews: ClickHouse, MotherDuck, Cube, dbt Labs, Starburst.

Kill line: kill if willingness to pay stays at 2 or fewer after 10 interviews, or the spike shows under 10% deflectable or cacheable queries, or at least 2 partners say they would buy the native layer instead. Any one condition is enough.

## Pricing, proposed

Tiered flat fee by warehouse spend, to test in pilots, not a list price:

- $500/mo: under $25k/yr warehouse spend
- $1,000/mo: $25k to $100k/yr
- $2,500/mo: over $100k/yr, plus a regulated premium where audit evidence is the value

Per-seat ($25/user/mo anchor) is a floor only. Per-query is a meter, not the headline. Percent of warehouse spend saved is the best direct-enterprise model and the worst self-serve model, because it needs before-and-after measurement on the customer's bill.

Year-3 scenarios from the Nov 6 report, all estimates with assumptions stated there: pessimistic $300k ARR (50 customers at $6k/yr), base $2.4M ARR (200 at $12k/yr), optimistic $18M ARR (600 at $30k/yr, including 2 OEM deals). Even the optimistic case barely pays for itself on measured savings alone unless the high deflection rate is real.

## Proposed architecture, not built

- Language: Rust, for a small, senior, systems-heavy data plane. SQL parsing with sqlparser-rs or equivalent, per dialect.
- Deployment: in-path proxy or library, inside the customer's trust boundary, near the warehouse. Not a hosted multi-tenant proxy that sees customer data by default.
- Engines: Snowflake, BigQuery, Databricks, Postgres, ClickHouse, DuckDB/MotherDuck, plus SaaS-backed tables.
- Components, planned: SQL parser and rewriter, policy engine (row/column, per-user ACL, masking), semantic layer compiler with refusal, cost estimator and cap enforcer, ACL-aware cache and dedup, audit log.
- What to measure first: the spike in the validation plan above. No component is built until the spike says the hit rate and overhead are real.

## Repo layout, planned

```
PROJECT.md        # this file
COMPETITORS.md    # vendor map, capability matrix, pricing anchors
README.md         # short version of PROJECT.md, for the repo front page
docs/
  spike.md        # 1-day spike plan and measured results
  interviews.md   # design-partner screening, questions, and anonymised outcomes
  pricing.md      # pilot pricing and measured savings per pilot
src/              # only after the spike passes
```

## Open questions

- Is pandata a company, a personal build, or a validation-only project? The Nov 9 review scored it 2/5 as a standalone company build. Do not default to a startup.
- Who are the first three design partners, by name, and will they share query logs?
- What is the exact name story? Working reading: pan (across, all engines) plus data. Confirm or correct.
- Who owns the semantic authoring per customer, and how long does it take? That is the bottleneck the data plane does not solve.
- What would change the score? Three partners committing to a paid pilot at $500/mo or more on their own bill, a data platform owner (not a CISO or AI lead) signing the pilot, and proof that a cross-engine estate is the norm, not the exception, in the target segment.

## Sources

- Nov 8, 2026 one-page pitch: "The governed query layer for agents", in `workspace/goals/governed-query-layer-for-agents/files/data-layer-pitch-one-pager/`
- Nov 6, 2026 quantitative market-gap report: `workspace/research_notes/mcp-market-gap-quantitative-20261106-1358/report.md`
- Nov 9, 2026 three-idea evaluation: governed query layer scored 2/5 as a standalone build, narrow wedge only
- Spider 2.0: Lei et al., arXiv:2411.07763. Leaderboard figures are unreliable after the Jan 2026 audit found annotation errors in 62.8% of Spider 2.0-Snow items, so do not quote them as product evidence.
