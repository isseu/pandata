# pandata

**The governed query layer for agents.**

Every agent that touches company data goes through pandata first. Pandata checks what the agent may ask, rewrites the question so it stays inside those limits, caps what it can spend, and says no when it should. It is not another agent. It is the layer underneath them, like Boat is for running code.

Status: we are still checking if this is worth building. No code yet, no customers yet, no pilot yet. Nothing here is a proven result. If a number sounds like a result, it is a target to test.

## The problem, in plain words

Agents are starting to query company data directly, and three things go wrong.

**They can see too much.** An agent can ask for the wrong tables, the wrong columns, or a full scan nobody approved.

**They can spend too much.** One bad question can scan a huge amount of data. Agents also repeat almost the same question all day, and pay for it each time.

**They can give a wrong number.** If nobody has written down what a metric means, an agent will guess. A confident wrong number is worse than no number.

Data teams feel this first, especially where agents already touch real data, or are blocked from doing so.

## What pandata does

When an agent sends a question, pandata does five things, in order, before the question reaches the database.

1. **Read and fix the question.** It reads the SQL, the database language, checks it against the rules, and rewrites it. It blocks banned joins, blocks questions that span systems they should not span, and removes columns the caller may not see.
2. **Check who is really asking.** The rules follow the person behind the agent, not just the agent. Pandata applies row and column rules, hides sensitive columns, and says no if the person does not have permission.
3. **Cap the cost.** It estimates the cost before running anything, and stops the question if it would cost too much.
4. **Reuse safe answers.** If the same question was already answered, pandata can reuse that answer, but only if the new caller had permission to see it. Permissions are part of the lookup, so a reused answer cannot leak data to the wrong person.
5. **Write it all down, and say no cleanly.** Every decision, rewrite, cap, reuse, and refusal is logged. Saying no is a normal outcome, not an error.

On top of this sits a list of business definitions, written by a person: what "revenue" means, what "active user" means, and so on. Pandata turns those definitions into SQL, and refuses questions that are outside them. That list is what makes answers right. Pandata does not write the list for you, and it does not claim to.

How a question flows:

```
agent asks a question
  -> pandata checks who is asking, and what they may see
  -> pandata reads and fixes the question, or says no
  -> pandata checks the cost, and checks for a safe reused answer
  -> the question runs, on Snowflake, BigQuery, Databricks, Postgres, ClickHouse, or DuckDB
  -> pandata logs what happened
```

## Who it is for

Companies with 50 to 500 people, running two or more of those systems at once, with no single tool that covers them all. They have someone who owns the database bill, and no team to build this themselves.

A good first customer has all three of these:

- At least two database systems in use, or one warehouse plus other tables agents query.
- An agent already touching real data, or blocked from doing so.
- A real database bill or query history they can show us, not just describe.

We are not for companies that use only Snowflake, or only Databricks. Their own tools already cover that, and they do it well. We are also not for companies with no agent using data yet.

Chile and Latin American banks and fintechs are a second, smaller starting point. There, the audit trail matters as much as the cost: Chile has 19 banks and about 40 authorised fintechs, a new data protection law in force from 1 Dec 2026, and Open Finance rules from July 2027. That is a good place to test, not a big market on its own.

## What it is not

- Not another agent or chatbot. It does not come up with the question. It checks the question.
- Not a generic gateway for AI tools. Those check which tool an agent may call. Pandata reads the actual database question.
- Not a tool that fixes accuracy by itself. Accuracy comes from the business definitions, written by a person. Pandata makes those definitions enforceable.
- Not a dashboard. It has no charts. It sits underneath other tools.

## Why it might work

From our research on 6 Nov 2026, in `COMPETITORS.md`:

- No vendor we looked at, about 25 of them, does all five things above, across several database systems. The closest are Dremio and Immuta, and they only do part of it.
- Nobody we found offers safe reuse of answers where permissions are part of the lookup. Warehouses reuse only exact copies of a question, and agents rarely ask the exact same question twice.
- The accuracy evidence points the same way. The same agent scored 91.2 percent on a clean test, Spider 1.0, and 21.3 percent on real company work, Spider 2.0. A hand-written list of definitions got dbt to 100 percent on the questions it covered, in a small test of 11 questions. The list matters. Pandata makes the list enforceable.

## Why it might not work

We scored this 29 out of 45 in that same research, below our own bar of 31 to go ahead and build. So we test first.

- **It may not pay for itself.** A typical company in our target spends about $24,000 a year on its warehouse. Even if we reuse 20 percent of agent questions, the saving is about $5,800 a year, less than a $12,000 a year price. It only works for bigger spenders, or if reuse is much higher, or if the audit trail is worth paying for on its own.
- **It is not clear who buys it.** The data team owns the bill, the security team owns the rules, and the AI team owns the agents. They are often three different people.
- **The big warehouses may close the gap.** Snowflake, Databricks, and ClickHouse all ship their own agent tools now. If they start covering several systems at once, our opening closes.
- **Writing the definitions is the hard part.** That work takes time per customer, and no amount of fast code removes it.
- **There is no lock-in.** The definitions belong to the customer. A competitor could add a cache or a cost cap in a few months. If we win, it will be by doing the work well and earning trust, not by a secret trick.

## How we test it, before building it

1. **A one-day test, on real query logs.** We measure how often questions repeat in a useful way, how many agent questions we can read and fix, and how much time we add. Pass if at least 20 percent of questions are reusable, and we add less than 20 milliseconds in the slow cases. Kill if under 10 percent are reusable.
2. **Ten interviews.** Five in Chile and Latin America, five in the US and Europe. We only count it as a win if at least three say they will pay for a pilot, on the basis of their own bill.
3. **Paid pilots at $500 a month.** Thirty days, measured on the customer's own logs. If reuse is under 10 percent, we refund the fee.
4. **Only then, talk to other vendors** about building this inside their product: ClickHouse, MotherDuck, Cube, dbt Labs, Starburst.

Kill it if 2 or fewer will pay after 10 interviews, or the one-day test shows under 10 percent reusable questions, or at least 2 people say they would just use the warehouse's own tool instead. Any one of those is enough.

## Price, to test

Not a final price, just what we test in pilots:

- $500 a month, if the warehouse bill is under $25,000 a year
- $1,000 a month, for $25,000 to $100,000 a year
- $2,500 a month, above $100,000 a year

In year three, our research estimates $300,000 a year in the low case, $2.4 million in the middle case, and $18 million in the high case, which needs other vendors selling it for us. Even the high case barely pays for itself on savings alone, unless reuse is much higher than we assume.

## What we would build, if the tests pass

- In Rust, a fast systems language, because this layer has to be quick and reliable.
- It runs inside the customer's own systems, close to their databases, not as a shared service that sees their data.
- First Snowflake and Postgres, then BigQuery, ClickHouse, and DuckDB.
- No text-to-SQL generation, no dashboard, no shared hosted service.

No part of this is built until the one-day test says the reuse rate and speed are real.

## Open questions

- Is this a company, a personal project, or just a test? Our review on 9 Nov scored it 2 out of 5 as a standalone company. Do not assume it is a startup.
- Who are the first three customers by name, and will they share query logs?
- Who writes the business definitions for each customer, and how long does that take?
- What would change our mind? Three customers paying for a pilot at $500 a month or more, a data team lead signing it, and proof that most target companies really do run two or more systems.

## Sources

- The one-page pitch from 8 Nov 2026, "The governed query layer for agents"
- The market research from 6 Nov 2026, in `workspace/research_notes/mcp-market-gap-quantitative-20261106-1358/report.md`
- The three-idea review from 9 Nov 2026
- Spider 2.0, Lei et al. Do not quote its leaderboard scores as proof: a Jan 2026 audit found errors in 62.8 percent of its Snow items.
