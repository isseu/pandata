# pandata roadmap

Repo: https://github.com/isseu/pandata
Companion files: `PROJECT.md`, `COMPETITORS.md`, `ELEVATOR_PITCH.md`
Status: validation only, as of 2026-11-10. No code, no prototype, no design partner, no paid pilot yet.

This roadmap is gated. Each phase ends with a decision: pass, extend once, or kill. No phase starts the next build phase until its gate is passed in writing. Dates are relative to a start of 2026-11-10 and assume Enrique is the only builder, with a Miami blackout 2026-11-25 to 2026-11-30. Move the dates if the start moves, do not compress the gates.

## At a glance

| Phase | What | When | Gate |
|---|---|---|---|
| 0 | Validation sprint, setup | Week 1, Nov 10 to 14 | Spike inputs ready, 10 interview targets named |
| 1 | 1-day technical spike | Day 1 to 2, in Week 1 | Normalised hit rate at or above 20%, p95 overhead under 20ms, rewrite coverage at or above 70% |
| 2 | 10 design-partner interviews | Weeks 2 to 3, Nov 17 to Dec 5, skipping Miami week | At least 3 will pay for a pilot, at least 1 data platform owner signs |
| 3 | Paid pilots, $500/mo | Weeks 4 to 8, Dec to Jan | Measured deflection at or above 10%, at least 2 renew or expand |
| 4 | MVP build, only if Phase 3 passes | Months 3 to 5 | Working in-path layer on 2 engines, with audit log and caps, in 2 pilot estates |
| 5 | Scale or OEM | Months 6 to 12, only if Phase 4 passes | Repeatable onboarding, or 1 OEM letter of intent |
| Kill | Any gate fails | Any time | Stop, write up what was learned, do not keep building |

Total validation time: 2 to 4 weeks for Phases 0 to 2, plus 4 weeks for Phase 3 pilots. First revenue realistically 3 to 6 months, not 4 weeks, because a credible pilot needs warehouse-bill measurement (30 days) plus semantic authoring per customer (weeks).

## Phase 0: Setup, Week 1, Nov 10 to 14

Goal: be ready to measure, not ready to demo.

Tasks:

- Freeze the scope in `PROJECT.md`: cross-engine, per-user ACL, five data-plane functions, semantic layer with refusal. No new scope in this sprint.
- Assemble spike inputs, no customer needed yet:
  - Public query logs, for example a dbt project's query history export, Snowflake sample queries, or Enrique's own warehouse history.
  - Spider 2.0 and BIRD sample schemas, for agent-style query generation.
  - A toy estate in Docker: DuckDB plus Postgres plus ClickHouse.
- Name 10 interview targets, screened, with a named contact and a reason they fit. Five Chile and LatAm financials, five US and EU mid-market SaaS or fintech. Do not start interviews until the spike is done, so the spike result shapes the questions.
- Pull the missing market numbers from the Nov 6 report: exact Census SUSB firm counts for 50 to 99, 100 to 249, and 250 to 499, and BigQuery customer-count proxies. Do not quote the modelled ICP count (about 13,000 central, 7,000 to 27,000 range) as a fact until then.
- Re-check the two stale pricing anchors live: Portkey under Palo Alto Networks, and Cloudflare AI Gateway.

Exit criteria:

- Spike inputs are in hand, toy estate runs, and the spike script is written before any data is run through it.
- 10 targets named, with screening notes.
- No code written beyond the spike harness.

## Phase 1: 1-day technical spike, Day 1 to 2 in Week 1

Goal: a measured hit rate, not a demo. This is the cheapest kill test, so it comes first.

Measure, on 1,000 real consecutive queries:

1. Cache hit rate. Normalise SQL with sqlparser-rs or equivalent: canonicalise literals, whitespace, and predicate order. Report exact-text hits vs normalised and semantic hits separately. Semantic means same tables, filters, and aggregation, different literal.
2. SQL rewrite coverage. Generate 50 agent-style queries with an LLM against the toy estate. Report percent that parse, that are aggregation-only, that contain a blocked join or cross-warehouse join, and that touch a denied column.
3. Latency overhead. p50 and p95 added milliseconds for parse plus rewrite plus cache lookup, on 1,000 queries. Target under 5ms p50, under 20ms p95.
4. Cap enforcement. Bytes and rows estimated pre-execution vs actual, using EXPLAIN or equivalent. Report percent of queries that would have exceeded a $0.50 cap, and whether the estimate caught them.

Gate:

- Pass: normalised hit rate at or above 20%, and p95 overhead under 20ms, and rewrite coverage at or above 70%.
- Kill: normalised hit rate under 10% and rewrite coverage under 70%.
- In between: extend once, by 2 days, to test on a second log source. Do not extend twice, and do not lower the bar to keep going.

Deliverable: `docs/spike.md`, with the log source, the exact normalisation rules, the raw counts, the latency numbers, and the pass or kill call. No cherry-picked queries. If the spike fails, the roadmap stops here.

## Phase 2: 10 design-partner interviews, Weeks 2 to 3

Goal: willingness to pay, from people who can show a bill, not from people who like the idea.

Who, screen in only if all three hold:

- Head of Data Platform, Staff Data Engineer, or Head of Analytics Engineering, at a 50 to 500 person firm.
- Runs at least 2 of Snowflake, BigQuery, Databricks, Postgres, ClickHouse, DuckDB or MotherDuck in production, or a warehouse plus at least 1 SaaS-backed table agents query.
- An AI agent or natural-language tool already touches production data, or is blocked from doing so by governance, not by lack of interest.
- Can show, not just describe, a warehouse bill or query-history export.

Screen out: single-warehouse Snowflake-only shops, firms with no agent usage, firms under 50 or over 500 people, consultants and vendors.

How many: 10. Five Chile and LatAm financials, five US and EU mid-market SaaS or fintech. Ten is enough to kill (0 to 1 hits) and enough to pass to a pilot (at least 3 hits). More than 10 before a spike result is wasted motion.

Questions, exact, from the Nov 6 report, section 6. Ask the same core questions to all 10, in the same order, and record the answers verbatim where possible:

- What engines do you run, and what does the warehouse bill look like, by engine, per month?
- Which agents or tools touch production data today, and what are they allowed to ask?
- What is blocked today, and who blocked it, and why?
- Show me the last month of query history. How many queries are near-duplicates, and how often does the same question get asked with a different literal or wording?
- What was the last bad query, and what did it cost, in money and in time to fix?
- Who owns the warehouse bill, who owns governance, and who owns agents? Are they the same person?
- What would you pay for, if the saving was measured on your own bill? What would you not pay for?
- Would you buy the native layer instead, and if so, what would change your mind?
- Would you run a 30-day paid pilot at $500 a month, measured on your own logs, with a refund if deflection is under 10 percent?

What counts as a hit: a named data platform owner, not a CISO or AI lead alone, commits in the interview to a paid pilot at $500 a month or more, on the basis of their own bill, and agrees to share query logs for the spike and pilot. A "sounds interesting" is not a hit. A referral to someone else is not a hit.

Gate:

- Pass: at least 3 hits, including at least 1 data platform owner signing the pilot, and at least 60 percent of interviewed firms run at least 2 engines, confirming the cross-engine ICP is the norm, not the exception.
- Kill: 2 or fewer will pay after 10 interviews, or at least 2 partners say they would buy the native layer instead.
- Extend once: if 2 hits and 2 more are close but need a second call, take 1 extra week and 2 extra interviews, no more.

Deliverable: `docs/interviews.md`, anonymised, with screening notes, the 10 answers in a table, the hit count, and the pass or kill call. Do not name a partner who has not agreed to be named.

## Phase 3: Paid pilots, Weeks 4 to 8

Goal: measured saving on the customer's own bill, not a demo in a sandbox.

Scope per pilot:

- 30 days, $500 a month, flat fee. If deflection is under 10 percent, the pilot fee is refunded.
- One estate per pilot, at least 2 engines, with the customer's real query logs and real permissions.
- Pandata runs inside the customer's trust boundary, near the warehouse. No multi-tenant hosted proxy that sees customer data by default.
- Semantic authoring: hand-author the business definitions for the pilot's in-scope questions, with the customer. Time-box it to 1 week per pilot. Record how long it actually took, that is a cost of the product, not a free extra.
- Measure weekly: normalised cache hit rate, deflection rate, warehouse spend before and after, LLM token spend before and after, caps hit, refusals, and any leaked rows (target zero, and stop the pilot if one occurs).

Gate:

- Pass: at least 2 of 3 pilots show deflection at or above 10 percent, with no leaked rows, and at least 2 renew or expand to a second month or a wider estate.
- Kill: deflection under 10 percent on 2 or more pilots, or a leaked row, or 0 renewals after the refund offer.
- Extend once: if 1 pilot is close but needs a second month of logs, extend that pilot only, by 30 days.

Deliverable: `docs/pricing.md`, with per-pilot measured savings, the actual semantic authoring time, the renewal decision, and the price that pilot would pay going forward. Do not annualise a 30-day pilot into an ARR claim without labelling it as an extrapolation.

Pricing to test in pilots, not a list price:

- $500/mo: under $25k/yr warehouse spend
- $1,000/mo: $25k to $100k/yr
- $2,500/mo: over $100k/yr, plus a regulated premium where audit evidence is the value

## Phase 4: MVP build, only if Phase 3 passes, Months 3 to 5

Goal: a working in-path layer on 2 engines, in 2 pilot estates, with the audit log and caps. Nothing more.

Scope, in order:

1. Rust data plane: SQL parser and rewriter (sqlparser-rs or equivalent, per dialect), policy engine (row and column, per-user ACL, masking), cost estimator and cap enforcer, ACL-aware cache and dedup, audit log.
2. Engines first: Snowflake and Postgres. Then BigQuery, then ClickHouse, then DuckDB and MotherDuck. Do not add an engine to win a pilot that is not already in the target segment.
3. Deterministic semantic layer: compile hand-authored definitions to SQL, with refusal for out-of-scope questions. Authoring UX is a separate workstream, not an afterthought, because it is the bottleneck.
4. Deployment: in-path proxy or library, inside the customer's trust boundary, near the warehouse. Embeddable from the start, so OEM is possible later without a rewrite.
5. Observability: per-query decision log, per-caller cost, cache hit rate, refusal rate, and a dashboard that shows the customer's own bill impact.

Explicitly not in the MVP:

- No text-to-SQL generation. Pandata governs queries, it does not generate them.
- No dashboard or BI tool. No chart, no natural-language chat UI.
- No hosted multi-tenant service that sees customer data.
- No single-warehouse optimisation. If a pilot is single-warehouse, it is out of scope.
- No new engine, no new policy model, and no AI feature that is not needed for the pilot's in-scope questions.

Gate:

- Pass: working in 2 pilot estates, with zero leaked rows in 30 days, caps held on every query that exceeded them, and p95 overhead under 20ms in production, not just in the spike.
- Kill: a leaked row that is not a test-data error, or p95 overhead over 50ms after 2 weeks of optimisation, or both pilots churn after the MVP month.
- Extend once: 2 weeks, for one engine or one performance fix, not for new scope.

Deliverable: a tagged release, `docs/spike.md` updated with production numbers, and a written decision on whether to continue to Phase 5.

## Phase 5: Scale or OEM, only if Phase 4 passes, Months 6 to 12

Goal: repeatable onboarding, or 1 OEM letter of intent. Not both at once. Pick one, and say which.

Direct path:

- Onboarding in under 2 weeks per customer, including semantic authoring, measured, not estimated.
- Repeatable pricing: tiered flat fee by warehouse spend, with the savings guarantee from Phase 3.
- Target: 28 customers at $300 a month for $100k ARR, or 200 customers at $1,000 a month for $2.4M ARR in Year 3, base case from the Nov 6 report. Do not quote the optimistic $18M ARR case without the OEM deals that case depends on.

OEM path:

- Targets, uncontacted until now: ClickHouse, MotherDuck, Starburst, Cube, dbt Labs. Do not target Snowflake or Databricks, they are capture risk, not a channel.
- Ask: an embeddable layer, $50k to $250k a year platform licence plus $0.001 to $0.005 per governed query, or 10 to 20 percent revenue share on the attach SKU. Worked example: an OEM with 4,000 customers, 10 percent attach, $6,000 a year attach price, 20 percent share, $480k a year to pandata.
- OEM cycles are 6 to 12 months. Do not plan revenue from OEM in the same quarter as the first conversation.

Gate:

- Pass: repeatable onboarding at or under 2 weeks, with at least 5 paying customers after the pilots, or 1 signed OEM letter of intent with a named attach price.
- Kill: onboarding stays over 4 weeks after 3 customers, or no OEM conversation reaches a second meeting after 5 outreach attempts, or a direct competitor ships the wedge first (see Competitors, "What would change this map").

## What would change the roadmap

- Dremio or Immuta announce ACL-aware semantic caching or per-agent cost caps: re-score the moat, and shorten Phase 4, because the window is closing.
- Snowflake, Databricks, or ClickHouse ship cross-engine governance: stop the direct path, and re-evaluate OEM only.
- A competitor publishes a measured cache hit rate on real agent logs, with methodology: that sets the bar for Phase 1, and the spike must beat it, not just pass 20 percent.
- Chile Law 21.719 is postponed to 1 Dec 2027, or the NCG 514 date moves: the Chile beachhead timing changes, but the US and EU interview plan does not.
- Credible Data, CData, or Celigo ship a governed query engine that covers cross-engine ACL-aware caching: add them to `COMPETITORS.md` with a live check, and re-run the capability matrix before Phase 4.

## Risks, and what mitigates them

- Warehouse-native capture. Mitigation: screen for cross-engine estates only, and do not spend time on single-warehouse pilots.
- Semantic authoring is the bottleneck. Mitigation: time-box it in Phase 3, measure it, and do not pretend the data plane solves it.
- Buyer is unclear. Mitigation: require a data platform owner to sign the pilot, not a CISO or AI lead alone.
- Price is too high for a gateway-shaped product. Mitigation: price as a warehouse-cost product, with the saving measured on the customer's own bill, and refund if deflection is under 10 percent.
- Cache leaks across users. Mitigation: permissions are part of the cache key, from the spike onward, and a leaked row kills the pilot.
- Enrique is the only builder, and Miami is Nov 25 to 30. Mitigation: validation is 2 to 4 weeks of interviews and measurement, not a build, and no phase depends on a single week.

## How to use this roadmap

- Before starting a phase, read its gate out loud and write down the pass number. Do not start the phase if the gate is vague.
- At the end of a phase, write the decision in `docs/` with the numbers, not in chat. Chat is not the record.
- If a gate fails, stop. Write up what was learned in `docs/`, update `PROJECT.md` with the outcome, and do not keep building to avoid admitting the kill.
- If a date slips, move the date, not the gate. A gate that moves to fit the result is not a gate.

## Sources

- Nov 6, 2026 quantitative market-gap report, sections 5 and 6: scorecard 29/45, pass bar 31/45, kill thresholds, spike plan, interview screening and questions, OEM targets.
- Nov 8, 2026 one-page pitch: Boat principles, five data-plane functions, first targets.
- Nov 9, 2026 three-idea evaluation: governed query layer scored 2/5 as a standalone company build, narrow wedge only, Credible Data $10M seed July 2026, CData and Celigo MCP governance Oct 2026.
- `PROJECT.md` for scope and pricing, `COMPETITORS.md` for the vendor map and what would change it, `ELEVATOR_PITCH.md` for what to say to each audience.
