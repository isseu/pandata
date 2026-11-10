# pandata competitors

As of 2026-11-06, from the quantitative market-gap report for the governed query layer wedge. Re-check live before quoting any price, funding round, or benchmark. Vendor claims are labelled as vendor claims. Estimates are labelled as estimates.

Companion file: `PROJECT.md`.

## Short version

- Generic MCP gateways are crowded and consolidating. Warehouse-native agent layers cover single-warehouse shops. That part is solved.
- The specific wedge is narrow: in-path SQL parse and rewrite, plus per-user ACL-aware caching, plus cost caps, across warehouses and Postgres / ClickHouse / DuckDB, plus a deterministic semantic layer with refusal.
- 0 of about 25 vendors reviewed do all five wedge functions. Two do three or more partially: Dremio and Immuta.
- No vendor documents ACL-aware semantic result caching with per-user keying as a product feature.
- The biggest threat is not a direct competitor. It is warehouse-native capture, and Dremio or Immuta adding a cache or cap feature.

## Capability matrix

Scored from each vendor's own documentation, not marketing. Full circle is documented, half is partial or single-engine or via native policy, empty is not found in docs.

| Vendor | In-path SQL parse/rewrite | Row/col policy, per user | ACL-aware exact + semantic cache | Bytes/row/cost caps | Cross-engine (warehouse + Postgres/ClickHouse/DuckDB) | Deterministic semantic layer + refusal |
|---|---|---|---|---|---|---|
| Snowflake Cortex / Analyst | No | Yes, native RBAC, semantic-view grants | No, native 24h exact-text result cache only | Partial, warehouse limits, credits | No, Snowflake only | Yes semantic views, no documented refusal, it guesses |
| Databricks Genie | No | Yes, Unity Catalog | No | Partial, DBU budgets | No, Databricks only | Partial, Genie Spaces and metric definitions, no documented refusal |
| BigQuery + MCP Toolbox | No, execute_sql is free-form, Toolbox is parameterised tools | Partial, BigQuery column ACLs via policy tags, Postgres via DB roles | No | No, no Toolbox cap documented | Partial, Toolbox covers many engines, no unified policy | No |
| ClickHouse Agents | No | Partial, ClickHouse RBAC | No | No | No, ClickHouse only | No |
| Immuta | Partial, rewrites via native Snowflake/Databricks row/column policies, not its own in-path parser | Yes, row/col/cell, ABAC | No, no caching in docs | No, no cost caps in docs | Partial, Snowflake, Databricks, BigQuery, Redshift, Synapse, Starburst, no Postgres/ClickHouse/DuckDB | No |
| Dremio (SAP) | Partial, federated engine rewrites for federation, not an agent-SQL guardrail | Yes, fine-grained row/col | Partial, Autonomous Reflections, intelligent caching, vendor claim 50% redundant-workload cut | Partial, $0.20/DCU, elastic, no per-agent byte cap documented | Yes, federated: SQL Server, MongoDB, Oracle, Postgres, lakes | Yes, AI Semantic Layer plus MCP server, closest partial |
| Starburst / Trino | Partial, federated query | Partial, Trino RBAC / SEP | No | No | Yes, federated | Partial, semantic layer exists, refusal not documented |
| Cube | No, compiles semantic definitions to SQL, does not police agent SQL | Partial, docs warn accessible-views is not a security control | Partial, pre-aggregations and caching | No | Yes, about 34 sources | Yes semantic layer, no refusal |
| dbt Semantic Layer | No, MetricFlow compiles metrics, does not police ad-hoc SQL | Partial, inherits warehouse grants | Partial, cache via warehouse | No | Partial, 6 platforms | Yes, plus refusal, the refusal evidence comes from dbt |
| ContextForge / agentgateway | No | Partial, tool-level RBAC / CEL policy | No | Partial, rate limits only | No, tool-level, not SQL-level | No |
| Runlayer / Noma / Arcade | No | Partial, tool-level approve/review/block, per-agent identity | No | Partial, budgets at tool level | No | No |
| CData Connect AI | No, live query pushdown, source-level permissions | Partial, source-level, inherited user permissions | Partial, caching listed as a platform capability | No | Yes, hundreds of SaaS and DB connectors | Partial, Context Engine / semantic context, new, limited access |

Count: 0 vendors do all five. 2 do three or more partially (Dremio, Immuta). 0 do ACL-aware semantic caching with per-user keying as a documented product feature.

Why the gap persists: SQL parsing is dialect-specific and unglamorous, gateway vendors are LLM and networking teams, not query-engine teams. ACL-aware caching is hard, the cache key must include the user's effective permissions plus data version, or it leaks. The safe default is not to cache across users, which is what warehouses do. And the buyer for the data plane is the data platform team, while the buyer for gateways is the AI platform or CISO team. Different budgets, different vendors.

## By layer

### 1. Warehouse-native agent layers: the capture risk

These win single-warehouse shops by default. They are the reason pandata screens single-warehouse firms out.

- Snowflake Cortex / Analyst. 13,328 customers, $4.68B FY26 revenue, 733 spending over $1M/yr, NRR 125%. Cortex Analyst API: 67 credits per 1,000 messages, $0.134 (Standard) or $0.201 (Enterprise) per question. Semantic-view cap about 1MB / 32k tokens, about 50 to 100 columns recommended. Vendor internal claim of 90% plus on a 150-question set is vendor-run, not independent evidence.
- Databricks Genie. Over $7B run-rate, over 20,000 organisations, over 1,000 spending over $1M/yr, Aug 2026. AI products $1.4B run-rate Feb 2026. Genie free for named users until 31 Jan 2027, then 150 DBU/user/mo free (about $10.50), then $0.070/DBU for LLM only. SQL compute billed separately.
- BigQuery plus Gemini and MCP Toolbox. No customer count disclosed, a material hole in the market sizing. On-demand $6.25 per TiB scanned, first 1 TiB/mo free. A Sep 2026 claim of a cut to $4.69/TiB was unconfirmed against the official page as read Nov 2026. Use $6.25.
- ClickHouse Agents. Over $250M cloud ARR, 4,000 customers, 27 May 2026. $400M Series D Jan 2026, $15B valuation Sep 2026. Agents launched May 2026, Claude-powered.

Both natives are effectively giving away the agent layer in 2026 to lock in warehouse consumption. Pandata cannot compete on agent price, only on cross-engine coverage.

### 2. Generic MCP gateways and registries: crowded

At least 12 named: ContextForge, agentgateway, Docker MCP Gateway, Microsoft MCP Gateway, Obot, MCPJungle, Unla, Kong, Envoy AI Gateway, ToolHive, MintMCP, lunar.dev / Bifrost. Registries: Smithery (now Arcade), Glama, PulseMCP, mcp.so. Mostly open source or $0 licence, commercial arms quote-only.

Open source gravity, point-in-time, stars move daily:

- LiteLLM: about 61,877 stars, 25 Oct 2026, 12,604 forks. Its README claim of 8ms P95 at 1k rps is unverified, no independent reproduction.
- ContextForge (IBM): about 4.3k stars, GA v1.0 Jun 2026.
- agentgateway (Linux Foundation): about 4.4k stars, Aug 2026 check, with an older compilation listing about 2,457, unresolved.
- Google MCP Toolbox: v1.14.0, Oct 2026.

Read: developer gravity is at the LLM gateway, not the MCP gateway. A Rust data plane cannot win on stars against LiteLLM. It has to win on a measurable warehouse-bill outcome.

### 3. LLM and AI gateways

At least 8: Portkey (now Palo Alto Networks), LiteLLM, Kong, Cloudflare, TrueFoundry, OpenRouter (now Stripe, Aug 2026), Helicone, Vercel AI Gateway.

Pricing anchors, verified where stated:

- CData Connect AI Standard: $79/mo annual ($99 monthly), 1 user / 1 source, vendor page.
- Composio Pro: $29/mo plus $0.0003/tool-call.
- TrueFoundry Pro: $25/user/mo.
- Portkey Production: $49/mo (100k logs), Oct 2026 compilations, stale risk after the Palo Alto acquisition.
- Helicone Pro: $79/mo. Gatana Pro: $125/mo/org.
- Cloudflare AI Gateway core is free. The "$7/user" figure in earlier notes is wrong as stated: about $7/mo is a modelled total at 50M requests/mo ($5 Workers Paid plus about $2 Logpush), not a per-user price.

These anchors cap gateway-shaped willingness to pay at roughly $350 to $1,500/yr for a team of 25. A $1,000/mo pandata price is 8 to 34 times that anchor, and can only be justified as a warehouse-cost product.

### 4. MCP security and agent control planes: the funded layer

At least 10: Runlayer, Noma, HiddenLayer, WitnessAI, Operant AI, Helmet, Manufact, Arcade, Cerbos / Permit (authz), Astrix (now Cisco). Plus agent security posture and non-human identity vendors: Noma, Astrix, Entro (now SailPoint), Oasis (now Cyera), Token, Clutch, Aembit.

Funding, verified where stated:

- Runlayer: $42M total, $11M seed Nov 2025 plus $30M Series A Jun 2026. Rounds sum to $41M, company states $42M.
- Noma: $132M total, $7M seed plus $25M Series A Oct 2024 plus $100M Series B Jul 2025.
- HiddenLayer: $156M total, via secondary table.
- WitnessAI: over $85M, via secondary blog.

These vendors do tool-level approve, review, and block, per-agent identity, and budgets at tool level. They never parse SQL. They are the checkpoint between agent and tool, not between agent and data.

Top-10 agentic-security funding of $3.6B in one blog aggregation is weak, treat as directional. Pure-play MCP security only is about $40M across 4 startups in the same source.

### 5. Data-access governance: the closest incumbents

At least 6: Immuta, Starburst, Privacera, Securiti, BigID, Cyera (data security).

- Immuta: $267M total, including $100M Series E Jun 2022. No new round found since 2022. Row, column, and cell policy with ABAC, but only via native Snowflake, Databricks, BigQuery, Redshift, Synapse, and Starburst policies. No Postgres, ClickHouse, DuckDB, or MotherDuck. No caching, no cost caps in its docs.
- Starburst: $414M total, including $250M Series D Feb 2022 at $3.35B valuation. Federated, Trino RBAC, semantic layer exists, refusal not documented.
- Privacera, Securiti, BigID, Cyera: governance and data security, not an in-path agent query plane. Funding not re-verified in this run, do not quote from memory.

### 6. Semantic layer and BI on warehouse: the accuracy side

At least 8: dbt Semantic Layer, Cube, AtScale, Looker / LookML, Sigma, Dremio, ThoughtSpot, GoodData.

- dbt Semantic Layer: MetricFlow compiles metrics, inherits warehouse grants, cache via warehouse. Refusal evidence comes from dbt: vendor benchmark 7 Apr 2026, n=11, raw text-to-SQL 32.7% (2023 model) to 64.5% (2026 models), semantic layer 72.7% on all 11, 100% in scope, 0% out of scope because it refused, vs raw 70 to 100% guessing wrong. Vendor-run, one dataset, directional, not lab-grade.
- Cube: compiles semantic definitions to SQL, about 34 sources, pre-aggregations and caching. Vendor benchmark Apr 2026, n=100, arXiv:2604.25149: 4KB hand-written semantic doc, plus 17 to 23 points to 67.7 to 68.7% across Claude Opus 4.7, Sonnet 4.6, and GPT-5.4. Tests a markdown doc, not a compiled semantic engine.
- Dremio: closest partial overall. Federated engine, fine-grained row and column policy, Autonomous Reflections and intelligent caching, vendor claim 50% redundant-workload cut, AI Semantic Layer plus MCP server. Now SAP-owned, a lakehouse-migration product, not an embeddable data plane. See M&A below.
- Sigma: $80M Series E May 2026 at $3B valuation, $200M ARR Apr 2026. Total raised conflicts: $661.3M vs $725.2M in different sources, do not quote a total without the conflict note.
- AtScale, Looker, ThoughtSpot, GoodData: semantic and BI layers, not an agent SQL guardrail.

Accuracy context: Spider 1.0, o1-preview code-agent 91.2%. Spider 2.0, same agent 21.3%, GPT-4o 10.1%. BIRD top system Oct 2026 82.95 vs human 92.96. BEAVER, off-the-shelf GPT-4o 0.0 to 2.0%, agentic frameworks about 10.8%. Spider 2.0 leaderboards Oct 2026, top agent 96.70 (Snow), 76.23 (Lite), 65.6 (DBT), are unreliable: a Jan 2026 expert audit found 62.8% of Spider 2.0-Snow items and 52.8% of BIRD Mini-Dev items have annotation errors, and re-scoring moved rankings up to 9 places. Do not use leaderboard scores as product evidence.

### 7. Connectors and data access

- CData Connect AI: hundreds of SaaS and DB connectors, live query pushdown, source-level permissions, caching listed as a platform capability, Context Engine for semantic context, new, limited access. Entry $79/mo annual.
- MotherDuck: about $100M Sep 2023 Series B at $400M valuation, plus about $33M extension May 2025, about $133M total, extension not confirmed by company release. DuckDB-native, small team, a plausible OEM target.
- DuckDB: 25,000 GitHub stars Dec 2024, 22M plus monthly PyPI downloads late 2025, secondary. Developer usage, not firm count.

### 8. Context and semantic competitors, newer

- SignalPilot: sells a self-improving context factory. Vendor claims: 96.9% on ADE-Bench against 39.5% for Claude Code alone, and 75% on Spider 2.0-DBT against the leaderboard's 65.6%, with cross-warehouse coverage, a fail-closed gateway, and pricing from $100/mo. Treat as vendor claims, not independent verification. Do not pitch a context factory as the wedge. The differentiation left to test is the underneath part it does not claim: parse and rewrite plus ACL-aware caching, dedup, and hard cost caps, sold embeddable, not as another context UI.
- Runlayer, Arcade, Natoma (now Snowflake), IBM ContextForge (free open source): all in the crowded gateway layer. See sections 2 and 4.

## M&A in 2026, verified where stated

| Deal | Date | Price / status |
|---|---|---|
| Portkey to Palo Alto Networks | Announced 30 Apr 2026, closed 29 May 2026 | $117M, PANW FY26 10-K via secondary. Becomes Prisma AIRS AI Gateway. |
| Natoma to Snowflake | Announced 27 May 2026, closed 3 Jun 2026 | $148.27M per California M&A filing aggregator, not in Snowflake's release, treat as secondary and unconfirmed. MCP-native non-human identity and agent governance. |
| Smithery to Arcade.dev | 5 Aug 2026 | Undisclosed. Connect and hosting shut down 31 Oct 2026, public directory remains. |
| Dremio to SAP | Announced 4 May 2026, closed 6 Jul 2026 | Terms undisclosed. SAP H1 2026 report: about 0.5B euros cash (about $571M) as the main portion of consideration, total price higher and undisclosed. Dremio valued at $2B in its 2022 $160M Series E, so this is likely a down-exit. |
| CyberArk to Palo Alto Networks | Closed 11 Feb 2026 | About $21.1B, PANW 10-K. Identity consolidation, context not wedge. |
| Protect AI to Palo Alto Networks | Closed 22 Jul 2025 | Reported about $650 to 700M. |
| OpenRouter to Stripe | Aug 2026 | Undisclosed. |
| Stack AI to Asana | May 2026 | About $75M, reported. |
| Observe to Snowflake | Feb 2026 | Undisclosed. |
| Neon to Databricks | May 2025 | About $1B, reported. Over 80% of Neon DBs created by AI agents, Databricks rationale. Postgres inside the lakehouse. |
| Crunchy Data to Snowflake | 2025 | $250M, reported. Postgres inside Snowflake. |

Pattern: 30 agent-security acquisitions since 2024, 14 in 2026 alone, per the Agent Security Map ledger via secondary, Sep 2026. Buyers are platforms (Palo Alto Networks, Snowflake, SAP, Cisco, Okta) buying the checkpoint between agent and tool. No 2026 deal bought a SQL-aware data plane. That is both the gap and the warning: no acquirer has validated the category with a cheque yet.

## Pricing anchors, for the pricing test

- Gateway anchors: $29 to $125/mo, $25/user/mo. See section 3.
- Warehouse unit costs, list, US regions, verified via Sep to Nov 2026 compilations reading vendor tables: Snowflake $2.00 / $3.00 / $4.00 per credit (Standard / Enterprise / Business Critical, AWS US-East), X-Small 1 credit/hr, billed per second, 60-second minimum. A 3-second query on a Large warehouse costs the same as 60 seconds. BigQuery $6.25 per TiB scanned. Databricks Serverless SQL $0.70/DBU-hr all-in.
- Typical query, estimate: a mid-market analyst query scanning 100 GB on BigQuery, 0.098 TiB times $6.25, about $0.61. On Snowflake X-Small at the 60s minimum, 1 credit, about $2 to $3 even if it runs 3s. A 10 TiB accidental scan, $62.50 on BigQuery. A firm running 5,000 agent queries/mo at $0.60 to $2.00 warehouse cost, $3,000 to $10,000/mo warehouse spend from agents alone, before LLM tokens.
- LLM token costs, Nov 2026 list, per 1M tokens: Claude Sonnet 5 $2 in / $10 out, Sonnet 4.6 $3/$15, GPT-5 $1.25/$10, GPT-5.5 Pro $30/$180. Worked cost per agent question, estimate: single-shot, 10k input plus 2k output on Sonnet 5, $0.04. Agentic multi-step, 5 to 10 model calls, $0.20 to $0.40 per question before tool and result tokens. At 10,000 questions/mo, $400 single-shot vs $2,000 to $4,000 agentic. The LLM bill, not the warehouse bill, dominates at low warehouse spend.
- Cache deflection, vendor claim only: Mosaic / Strategy, 6 Mar 2026, fleet-wide, 48.9% of queries deflected by in-memory semantic cache, average saving $63k/yr (spend $250k to $1M), $609k/yr ($1 to 5M), $4M/yr ($5 to 25M). Self-reported, no methodology published, vendor sells the cache. Use only as an upper bound. Pandata scenarios use 10 to 30% deflection.

## What would change this map

Watch for, and re-check before acting:

- Dremio or Immuta announcing ACL-aware semantic caching or per-agent cost caps. That would cut the moat score by 2 and close the closest partial gap.
- Snowflake, Databricks, or ClickHouse shipping cross-engine governance, or a native layer that spans Postgres. That would move the capture risk from severe to fatal for the cross-engine wedge.
- A warehouse-native layer adding refusal for out-of-scope questions, or a semantic layer with in-path enforcement. That would remove the refusal edge.
- A competitor publishing a measured cache hit rate on real agent query logs, with methodology. That would set the bar pandata has to beat in the 1-day spike.
- Pricing moves: Genie free period ending 31 Jan 2027, Portkey pricing under Palo Alto Networks, Cloudflare AI Gateway pricing. Re-check live.

## OEM targets and non-targets

Targets, uncontacted, for outreach only after the spike and interviews: ClickHouse (no SQL guardrail layer, Agents just launched, 4,000 customers), MotherDuck (DuckDB-native, small team), Starburst (federated but no agent guardrail), Cube and dbt Labs (semantic layer without an in-path enforcement plane), Hex and Sigma (BI and agent surface without cross-engine enforcement).

Do not target Snowflake or Databricks as OEM. They are the capture risk, not the channel.

Plausible OEM structure, estimate, no public OEM price list exists for this category: $50k to $250k/yr platform licence plus $0.001 to $0.005 per governed query, or 10 to 20% revenue share on the attach SKU. Worked: an OEM with 4,000 customers times 10% attach times $6,000/yr attach price times 20% share, $480k/yr to the wedge vendor. At 5% attach, $240k/yr. OEM is not a shortcut to scale. It is a slow, high-trust enterprise sale with 6 to 12 month cycles.

## Sources

- Nov 6, 2026 quantitative market-gap report, `workspace/research_notes/mcp-market-gap-quantitative-20261106-1358/report.md`, sections 1 to 6. All funding, pricing, M&A, benchmark, and capability figures above come from that report unless labelled otherwise.
- Nov 8, 2026 one-page pitch, "The governed query layer for agents".
- Nov 9, 2026 three-idea evaluation: generic MCP-for-DB is solved and crowded, warehouse-native MCP is free or bundled, Runlayer $42M, Arcade $72M, Natoma acquired by Snowflake May 2026, IBM ContextForge free open source, SignalPilot $100/mo anchor.
- Spider 2.0: Lei et al., arXiv:2411.07763. Jan 2026 audit, Jin et al., annotation errors in 62.8% of Spider 2.0-Snow items.
