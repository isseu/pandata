# Who else does this?

As of 6 Nov 2026, from our market research. Check prices, funding, and benchmarks live before quoting them to anyone. Where a number comes from the vendor itself, we say so.

## The short version

Most of this market is already solved, and not by us.

- Generic tools that connect agents to databases are crowded. Many are free.
- Snowflake, Databricks, and the other warehouses already cover companies that use only their system.
- What nobody does, as far as we found, is all five things pandata does, across several database systems at once: read and fix the actual question, enforce rules for the real person asking, cap the cost, safely reuse answers, and refuse when a question is out of scope.

That is the opening. It is narrow, and the big warehouses are the main threat to it, not a small startup.

## The five things, and who does them

"Yes" means we found it in the vendor's own docs. "Partly" means it works for one system only, or through another tool, or only in a limited way. "No" means we did not find it.

| Vendor | Reads and fixes the question | Rules for the real person | Safely reuses answers | Caps the cost | Works across systems | Refuses out-of-scope questions |
|---|---|---|---|---|---|---|
| Snowflake Cortex | No | Yes | No | Partly | No, Snowflake only | No |
| Databricks Genie | No | Yes | No | Partly | No, Databricks only | No |
| BigQuery + MCP Toolbox | No | Partly | No | No | Partly | No |
| ClickHouse Agents | No | Partly | No | No | No, ClickHouse only | No |
| Immuta | Partly | Yes | No | No | Partly | No |
| Dremio (SAP) | Partly | Yes | Partly | Partly | Yes | Yes |
| Starburst / Trino | Partly | Partly | No | No | Yes | Partly |
| Cube | No | Partly | Partly | No | Yes | No |
| dbt Semantic Layer | No | Partly | Partly | No | Partly | Yes |
| Generic gateways (Runlayer, Noma, Arcade, ContextForge) | No | Partly, at the tool level | No | Partly | No | No |
| CData Connect AI | No | Partly | Partly | No | Yes | Partly |

Nobody does all five. Dremio and Immuta come closest, and only in part.

Why is that gap there? Reading database questions is fiddly, different for each system, and not glamorous. Safely reusing answers is hard: if you reuse an answer without checking permissions, you leak data. So most tools simply do not reuse answers across people. And the people who buy a gateway are usually not the same people who own the database bill.

## The warehouses: the real competition

These win by default if a company uses only one of them. That is why we do not target those companies.

**Snowflake Cortex.** Turns plain language into SQL, but only over definitions you write in Snowflake. Rules come from Snowflake itself. It costs about $0.13 to $0.20 per question through its API. It does not read and fix an agent's question, it writes its own. It reuses only exact copies of a question, for 24 hours. It does not say no, it guesses.

**Databricks Genie.** Same idea, inside Databricks, governed by Unity Catalog, its permissions system. Free for named users until 31 Jan 2027, then a small free allowance, then about $0.07 per unit of model use, plus the database cost on top. Databricks only.

**BigQuery.** Google's warehouse, $6.25 per terabyte scanned, first terabyte a month free. Its agent tools do not check the question in the way pandata would, and there is no cost cap in the tool itself.

**ClickHouse Agents.** ClickHouse's own agent layer, launched May 2026. ClickHouse only, with its own permissions system.

All four are effectively giving the agent layer away to keep you on their warehouse. We cannot compete on price with them. We can only compete where a company uses several systems, and none of them covers the whole estate.

## The gateways: crowded

These are tools that connect agents to other tools and databases, and check which tool an agent may call. At least 12 of them exist, most are open source or free, including ContextForge from IBM, agentgateway, Docker's gateway, and Microsoft's.

The funded ones in this area are mostly about security, not databases: Runlayer raised $42 million, Noma $132 million. They approve, review, or block what an agent does, at the tool level. They never read the database question.

Price anchors here are low: $29 to $125 a month for a gateway, or $25 per user a month. That is why a $1,000 a month pandata price can only be justified by saving money on the database bill, not by being a gateway.

## The governance tools: closest, but not the same

**Immuta** ($267 million raised) sets row, column, and cell rules, but only by using the warehouse's own rules, for Snowflake, Databricks, BigQuery, Redshift, and a few others. No Postgres, ClickHouse, or DuckDB. No answer reuse, no cost caps in its docs.

**Starburst** ($414 million raised) can query across systems, but does not police agent questions, and does not document refusal.

**Dremio**, now owned by SAP, is the closest overall. It queries across systems, sets fine-grained rules, reuses work through its Reflections feature, and has a semantic layer plus an agent tool. But it is a lakehouse migration product, not a small layer you can embed inside another product. SAP bought it in July 2026.

## The definition tools: where accuracy lives

**dbt** and **Cube** are the tools that turn business definitions into SQL. They are where accuracy comes from, and they prove the point:

- dbt, in its own small test of 11 questions, got 100 percent right on the questions its definitions covered, and refused the ones outside them, instead of guessing. Raw text-to-SQL guessed wrong 70 to 100 percent of the time on those out-of-scope questions.
- Cube, in a test of 100 questions, gained 17 to 23 points from a short hand-written definitions document.

Both are vendor-run and small, so treat them as direction, not proof. But they show that definitions, written by a person, are what make answers right. Neither dbt nor Cube polices an agent's free-form question in the way pandata would.

**SignalPilot** is newer: it sells business context and governance, from $100 a month, and claims 96.9 percent on one benchmark and 75 percent on another. Those are its own claims, not independently checked. It does not show SQL reading and fixing, safe answer reuse, or cost caps on its homepage. It confirms that context is valuable, and it sets a low price floor for selling context alone.

**Credible Data** raised a $10 million seed in July 2026 for a governed query engine. **CData** and **Celigo** added governance for agent tools in Oct 2026. The governed-query space is getting crowded, not just the gateway space.

## What it costs to run a question

Useful numbers, from vendor price lists as read in Sep to Nov 2026:

- Snowflake: $2 to $4 per credit, with a 60-second minimum charge even if a question runs for 3 seconds. A typical small question costs about $2 to $3.
- BigQuery: $6.25 per terabyte scanned. A question scanning 100 GB costs about $0.61. A 10 terabyte accidental scan costs $62.50.
- Databricks serverless SQL: $0.70 per unit-hour, all in.
- Model cost: a simple agent question costs about $0.04 in model tokens. A multi-step agent question costs $0.20 to $0.40. At 10,000 questions a month, that is $400 versus $2,000 to $4,000. At low database spend, the model bill matters more than the database bill.

A company running 5,000 agent questions a month spends roughly $3,000 to $10,000 a month on the database alone, before model costs.

One vendor claims 48.9 percent of questions can be reused through a cache. That is its own claim, with no method published. We plan on 10 to 30 percent, and we test it on real logs before believing any number.

## Deals in 2026

The big platforms are buying the checkpoint between agent and tool, not the database layer:

- Portkey to Palo Alto Networks, $117 million, closed May 2026
- Natoma to Snowflake, about $148 million, closed June 2026, figure unconfirmed in Snowflake's own release
- Smithery to Arcade, Aug 2026, price not disclosed
- Dremio to SAP, closed July 2026, about $571 million in cash as the main part, total price not disclosed
- Neon to Databricks, about $1 billion, May 2025
- Crunchy Data to Snowflake, $250 million, 2025

No deal in 2026 bought a SQL-aware data layer like pandata. That is both the opening and a warning: no big buyer has validated this category with a cheque yet.

## What would change this

Watch for these, and check them live before acting:

- Dremio or Immuta add safe answer reuse or per-agent cost caps. That closes much of the gap.
- Snowflake, Databricks, or ClickHouse ship governance that spans several systems. That would be fatal for the cross-system opening.
- A warehouse tool adds refusal for out-of-scope questions. That removes one of our edges.
- A competitor publishes a measured reuse rate on real agent logs, with method. That sets the bar our one-day test has to beat.
- Genie stops being free on 31 Jan 2027, or Portkey changes price under Palo Alto Networks.

## Who we would sell through, and who we would not

Possible partners, not contacted yet, only after the test and interviews: ClickHouse, MotherDuck, Starburst, Cube, dbt Labs. A rough guess at a deal: $50,000 to $250,000 a year, plus a small amount per question, or 10 to 20 percent of what they charge for it. That kind of deal takes 6 to 12 months, and is not a shortcut.

Do not target Snowflake or Databricks as partners. They are the competition, not the channel.

## Sources

- Market research, 6 Nov 2026, sections 1 to 6
- The one-page pitch, 8 Nov 2026
- The three-idea review, 9 Nov 2026
- Spider 2.0, Lei et al. Its leaderboard scores are unreliable: a Jan 2026 audit found errors in 62.8 percent of its Snow items.
