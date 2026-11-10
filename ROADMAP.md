# Roadmap

Status: we are still checking if this is worth building. No code, no customers, no pilot yet.

This roadmap has gates. Each step ends with a decision: go, extend once, or stop. Do not start the next step until the gate is passed. If a date slips, move the date, not the gate.

Start: 10 Nov 2026. Miami blackout: 25 to 30 Nov. Enrique is the only builder for now.

## At a glance

| Step | What | When | Pass if |
|---|---|---|---|
| 0 | Get ready to measure | Week 1, 10 to 14 Nov | Test inputs ready, 10 interview targets named |
| 1 | One-day test on real logs | Day 1 to 2, Week 1 | At least 20% of questions reusable, and we add under 20ms in slow cases |
| 2 | Ten interviews | Weeks 2 to 3, 17 Nov to 5 Dec | At least 3 will pay for a pilot |
| 3 | Paid pilots, $500 a month | Weeks 4 to 8, Dec to Jan | Reuse at or above 10%, at least 2 renew |
| 4 | Build the first version, only if pilots pass | Months 3 to 5 | Works on 2 systems, in 2 customer estates, no leaks |
| 5 | Grow, or sell through other vendors, only if step 4 passes | Months 6 to 12 | Onboarding under 2 weeks, or 1 vendor deal in writing |

Testing takes 2 to 4 weeks. Pilots take another 4 weeks. First real revenue is realistically 3 to 6 months away, not 4 weeks, because a pilot needs 30 days of bills plus time to write the business definitions.

## Step 0: Get ready, Week 1

Goal: be ready to measure, not ready to show a demo.

- Freeze the scope: several systems, rules for the real person, the five things in `PROJECT.md`. No new scope in this test.
- Gather test material, no customer needed yet: public query logs, or Enrique's own history, plus sample company data, plus a small test setup with DuckDB, Postgres, and ClickHouse.
- Name 10 interview targets, with a named contact and why they fit. Five in Chile and Latin America, five in the US and Europe. Do not start interviews until the one-day test is done.
- Fill the two gaps in our research: the exact count of US companies with 50 to 499 people, and a better count of BigQuery customers. Do not quote our rough estimate of about 13,000 target companies as a fact until then.
- Re-check two prices live: Portkey under Palo Alto Networks, and Cloudflare's gateway.

Done when: the test material is in hand, the test setup runs, the test is written before any data runs through it, and the 10 targets are named. No product code yet.

## Step 1: One-day test

Goal: a measured number, not a demo. This is the cheapest way to kill the idea, so it comes first.

On 1,000 real questions in a row, measure:

1. **How often do questions repeat in a useful way?** Clean up the question first: same tables, same filters, same sums, different names or numbers count as the same question. Report exact copies and cleaned-up matches separately.
2. **How many agent questions can we read and fix?** Make 50 agent-style questions against the test setup. How many can we read, how many are sums only, how many contain a banned join, how many touch a column they should not?
3. **How much time do we add?** Target under 5 milliseconds typically, under 20 milliseconds in slow cases.
4. **Do our cost guesses work?** Estimate the size and cost before running, compare with what actually happened. How many questions would have passed a $0.50 cap, and did we catch them?

Pass if: at least 20 percent reusable, under 20 milliseconds in slow cases, and at least 70 percent of agent questions readable and fixable.

Stop if: under 10 percent reusable, and under 70 percent readable.

In between: extend once, by 2 days, on a second set of logs. Do not extend twice, and do not lower the bar.

Write it up in `docs/spike.md`: where the logs came from, how we cleaned the questions, the raw counts, the times, and the go or stop call. No cherry-picked questions. If this fails, the roadmap stops here.

## Step 2: Ten interviews

Goal: will anyone pay, from people who can show a bill, not just people who like the idea.

Talk to a data team lead, staff data engineer, or head of analytics, at a company with 50 to 500 people, who:

- Runs at least two systems in use, or one warehouse plus other tables agents query.
- Has an agent already touching real data, or blocked from doing so.
- Can show a database bill or query history.

Do not talk to: companies using only Snowflake, companies with no agent using data, companies under 50 or over 500 people, consultants, or vendors.

Ten is enough: 0 to 1 yeses kills it, 3 or more yeses moves it to pilots. More than 10 before the test result is wasted effort.

Ask the same core questions to all ten, in the same order:

- What systems do you run, and what is the bill for each, per month?
- Which agents touch real data today, and what may they ask?
- What is blocked today, who blocked it, and why?
- Show me last month's questions. How many are near-copies, and how often is the same question asked with different wording?
- What was the last bad question, and what did it cost, in money and time?
- Who owns the bill, who owns the rules, and who owns the agents? Is it the same person?
- What would you pay for, if the saving was measured on your own bill? What would you not pay for?
- Would you just use the warehouse's own tool instead, and what would change your mind?
- Would you run a 30-day paid pilot at $500 a month, measured on your own logs, with a refund if reuse is under 10 percent?

A yes counts only if a data team lead, not just a security or AI lead, commits to a paid pilot at $500 a month or more, on the basis of their own bill, and agrees to share query logs. "Sounds interesting" does not count. A referral does not count.

Pass if: at least 3 yeses, including at least 1 data team lead, and at least 6 of the 10 run two or more systems.

Stop if: 2 or fewer will pay, or at least 2 say they would just use the native tool.

Extend once: if 2 yeses and 2 more are close, take 1 extra week and 2 extra interviews, no more.

Write it up in `docs/interviews.md`, without naming anyone who has not agreed to be named: who we screened in and out, the answers in a table, the count, and the go or stop call.

## Step 3: Paid pilots

Goal: a measured saving on the customer's own bill, not a demo in a sandbox.

Each pilot:

- 30 days, $500 a month. If reuse is under 10 percent, we refund the fee.
- One estate, at least two systems, with the customer's real questions and real permissions.
- Pandata runs inside the customer's own systems, close to their databases. No shared service that sees their data.
- We write the business definitions together with the customer, time-boxed to 1 week. Write down how long it actually took: that is a cost of the product.
- Measure weekly: reuse rate, database spend before and after, model spend before and after, caps hit, refusals, and any leaked rows. Target zero leaks, and stop the pilot if one happens.

Pass if: at least 2 of 3 pilots show reuse at or above 10 percent, with no leaks, and at least 2 renew or expand.

Stop if: reuse under 10 percent on 2 or more pilots, or a leak, or nobody renews.

Extend once: if 1 pilot is close, extend that pilot only, by 30 days.

Write it up in `docs/pricing.md`: saving per pilot, time spent writing definitions, whether they renewed, and what price they would pay next. Do not turn a 30-day pilot into a yearly revenue claim without saying it is an extrapolation.

Price to test, not a final price: $500 a month under $25,000 a year in database spend, $1,000 a month for $25,000 to $100,000, $2,500 a month above that.

## Step 4: Build the first version, only if pilots pass

Goal: a working layer on 2 systems, in 2 customer estates, with a log and cost caps. Nothing more.

In order:

1. The core, in Rust: read and fix questions, enforce rules, estimate cost and cap it, reuse safe answers, write the log.
2. Systems first: Snowflake and Postgres. Then BigQuery, ClickHouse, DuckDB.
3. The definitions list: turn hand-written definitions into SQL, and say no to out-of-scope questions. The screen for writing definitions is its own piece of work, because it is the bottleneck.
4. It runs inside the customer's systems, and is built so another vendor could embed it later.
5. It shows, per question, what was decided, what it cost, how often answers were reused, and how often it said no.

Not in the first version:

- No generating questions from plain language. Pandata checks questions, it does not write them.
- No dashboard or chat screen.
- No shared hosted service that sees customer data.
- No work to optimise for a single-system customer. If a pilot is single-system, it is out of scope.
- No new system, no new rule type, and no new AI feature that the pilots did not need.

Pass if: it works in 2 estates, with zero leaks in 30 days, caps held on every question that passed them, and under 20 milliseconds added in slow cases, in real use, not just in the test.

Stop if: a real leak, or over 50 milliseconds in slow cases after 2 weeks of fixing speed, or both pilots leave after the first month.

Extend once: 2 weeks, for one system or one speed fix, not for new scope.

Ship a tagged release, update `docs/spike.md` with the real numbers, and write down whether we go to step 5.

## Step 5: Grow, or sell through other vendors, only if step 4 passes

Pick one. Not both at once.

**Grow direct:** onboarding under 2 weeks per customer, including writing definitions, measured, not guessed. Target: 28 customers at $300 a month for $100,000 a year, or 200 at $1,000 a month for $2.4 million a year in year three. Do not quote the $18 million high case without the vendor deals it depends on.

**Sell through vendors:** talk to ClickHouse, MotherDuck, Starburst, Cube, dbt Labs. Not Snowflake or Databricks, they are the competition. A rough deal: $50,000 to $250,000 a year, plus a small amount per question, or 10 to 20 percent of what they charge. Those deals take 6 to 12 months. Do not plan revenue from them in the same quarter as the first call.

Pass if: onboarding is under 2 weeks, with at least 5 paying customers after the pilots, or 1 vendor deal in writing with a named price.

Stop if: onboarding stays over 4 weeks after 3 customers, or no vendor conversation gets to a second meeting after 5 tries, or a competitor ships this first.

## What would change this plan

- Dremio or Immuta add safe answer reuse or per-agent cost caps: shorten step 4, the window is closing.
- Snowflake, Databricks, or ClickHouse ship rules that span several systems: stop the direct plan, look at vendor deals only.
- A competitor publishes a measured reuse rate on real logs, with method: that becomes the bar for step 1, not just 20 percent.
- Chile's data law is delayed to Dec 2027, or Open Finance moves: the Chile timing changes, the US and Europe plan does not.
- Credible Data, CData, or Celigo ship a governed query engine covering several systems with safe reuse: add them to `COMPETITORS.md` with a live check, and redo the comparison before step 4.

## Risks

- **The warehouses close the gap.** We only work with mixed estates, and do not spend time on single-system pilots.
- **Writing definitions is the bottleneck.** We time-box it in step 3, measure it, and do not pretend code removes it.
- **It is not clear who buys.** We require a data team lead to sign the pilot, not a security or AI lead alone.
- **The price is too high for a gateway.** We price it as a database-cost product, measured on the customer's own bill, with a refund if reuse is under 10 percent.
- **A reused answer leaks.** Permissions are part of the lookup from step 1 onward, and a leak stops the pilot.
- **One builder, and a trip to Miami.** Validation is interviews and measurement, not building, and no step depends on a single week.

## How to use this file

- Before a step, read its pass number out loud and write it down. Do not start if the gate is vague.
- At the end of a step, write the decision in `docs/` with the numbers. Chat is not the record.
- If a gate fails, stop. Write up what we learned, update `PROJECT.md`, and do not keep building to avoid admitting it.
- If a date slips, move the date, not the gate. A gate that moves to fit the result is not a gate.

## Sources

- Market research, 6 Nov 2026, sections 5 and 6: score 29 out of 45, bar 31, kill rules, test plan, interview questions, vendor targets
- The one-page pitch, 8 Nov 2026
- The three-idea review, 9 Nov 2026: scored 2 out of 5 as a standalone company, Credible Data $10 million seed July 2026, CData and Celigo governance Oct 2026
- `PROJECT.md` for scope and price, `COMPETITORS.md` for who else does this, `ELEVATOR_PITCH.md` for what to say
