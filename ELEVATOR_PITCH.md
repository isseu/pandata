# How to explain pandata

Read these out loud. They are written to be spoken, not read off a slide. Short and plain, no jargon where plain words will do.

Status: we are still checking if this is worth building. No code, no customers, no pilot yet. Do not claim a result we have not measured.

## The one line

Pandata is the governed query layer for agents. Not another agent, the layer every agent has to go through to touch company data.

## In 15 seconds

Agents need to touch company data, and right now they do it with no guardrails. Pandata sits in the middle, checks every question against who is asking and what they may see, caps what it can spend, and says no when it should. That is the whole idea.

## In 30 seconds, to a data team lead

Your agents query Snowflake, Postgres, and ClickHouse, and no single tool covers all three. The warehouses only cover their own system. Pandata is one layer that reads and fixes the question, enforces the rules for the real person behind the agent, caps the cost before anything runs, and reuses safe answers. If a question is outside what is defined, it says no instead of giving a wrong number. We prove it first on your own query logs, with a 30-day paid pilot.

## In 30 seconds, to a security lead

The risk is not the model. It is the question. An agent can ask for the wrong tables, join systems it should not join, and return a wrong number that looks right. Pandata puts the rules in the question itself, not in a prompt. It checks who is asking, hides columns they may not see, blocks banned joins, enforces a hard cost cap, and logs every decision. Nothing is reused across people unless the permissions allow it. That log is the audit trail.

## In 30 seconds, to a founder or investor

Do not build another agent. Build the layer every agent must go through to touch company data. That is the Boat model: cheap, fast, invisible, measurable, and easy to embed in other products.

The opening is narrow: companies running several database systems, where no warehouse tool spans them, plus a list of business definitions with a clear no for out-of-scope questions. Our research found no vendor doing all five parts of that, out of about 25 we looked at. It also found the money is tight: savings alone do not pay for a $12,000 a year price for a typical customer. So we test before we build: a one-day test on real logs, ten interviews, then paid pilots at $500 a month. We stop if 2 or fewer will pay, or under 10 percent of questions turn out reusable.

## In 60 seconds

Agents are being blocked, or quietly running up database bills, for the same reason: nobody governs the questions they ask.

Pandata is one layer in front of all the databases. An agent sends a question. Pandata reads it, checks it against the business definitions and the caller's real permissions, fixes it so it stays inside those limits, checks the cost, and reuses a safe answer if one exists. If it is not allowed, or not defined, it says no.

It is for companies with 50 to 500 people, running two or more systems, with no team to build this themselves. If you use only Snowflake, use their tool. If you use only Databricks, use theirs. We only help where the estate is mixed.

The edge is not making the agent smarter. On real company work, the same agent fell from 91 percent on a clean test to 21 percent. A hand-written list of definitions got dbt to 100 percent on the questions it covered, in a small test. The list is what makes answers right. Pandata makes the list enforceable, and makes saying no happen before money is spent or data leaks.

We are not building the full product yet. First the one-day test, then ten interviews, then pilots. If the numbers do not hold, we stop.

## Answers to the questions you will get

**"Is this not just a gateway for AI tools?"**
No. Those check which tool an agent may call. They never read the database question. Pandata reads the question.

**"Snowflake or Databricks already does this."**
Only for their own system. If you use only one of them, use their tool. We only help where you use several, and no native tool spans them. That is also our main risk: if they start spanning several systems, our opening closes.

**"How accurate is it?"**
We do not claim a number. Accuracy comes from the definitions, written by a person, not from our layer. What we do is enforce those definitions, say no to out-of-scope questions instead of guessing, and make the governed question cheap.

**"What does it cost?"**
To test in pilots, not a final price: $500 a month if the database bill is under $25,000 a year, $1,000 a month for $25,000 to $100,000, $2,500 a month above that. Pilots are $500 a month, with a refund if reuse is under 10 percent.

**"Who is it for first?"**
A data team lead at a 50 to 500 person company, running at least two systems, with an agent already touching real data or blocked from doing so, who can show us a database bill or query history.

**"Why now?"**
Agents are moving into real data faster than the rules are. Gartner expects over 40 percent of agent projects to be cancelled by the end of 2027, on cost, unclear value, and weak risk controls. In Chile, a new data protection law is in force from 1 Dec 2026, and Open Finance rules from July 2027. Neither proves anyone will pay. That is what the interviews are for.

**"Who is the competition?"**
Closest are Dremio, now owned by SAP, and Immuta. Neither offers safe answer reuse with permissions as part of the lookup, or cost caps, in its docs. The crowded side is generic gateways and the warehouses themselves. See `COMPETITORS.md`.

## What not to say

- Do not claim a reuse rate, a saving, a speed, or an accuracy score. We have not measured any of them yet.
- Do not quote Spider 2.0 leaderboard scores. A Jan 2026 audit found errors in 62.8 percent of its Snow items.
- Do not say this layer fixes accuracy. It does not. The definitions do, and writing them per customer is the real work.
- Do not pitch it as a company yet. Our review scored it 2 out of 5 as a standalone company. It is a test with a clear stop point.
- Do not pitch companies that use only one warehouse. That is the warehouses' market, and we lose there.

## The ask

- Data team lead: 30 minutes, and a query history or database bill to test against. No install, no access to live data.
- Security lead: 30 minutes, to walk through the rules, the reuse lookup, and the log.
- Design partner: an interview, then a $500 a month pilot if the test holds, measured on your own logs.
- Other vendors (ClickHouse, MotherDuck, Cube, dbt Labs, Starburst): a conversation after the test and interviews, about building this inside their product. Not a sales pitch now.

## To paste into an email

Subject: pandata, the governed query layer for agents

Agents need to touch company data, and right now they do it with no guardrails.

Pandata is one layer every agent has to go through. It reads and fixes the question, enforces the rules for the real person behind the agent, caps the cost before anything runs, reuses safe answers, and says no when a question is not allowed or not defined. It logs every decision.

It is for companies with 50 to 500 people running two or more database systems, where no native tool spans them. It is not another agent, not a generic gateway, and not a warehouse tool.

We are testing before building: a one-day test on real query logs, ten interviews, then $500 a month paid pilots. We stop if 2 or fewer will pay after ten interviews, or under 10 percent of questions turn out reusable.

If you run a mixed estate and an agent already touches it, or is blocked from doing so, I would like 30 minutes and a query history to test it against.
