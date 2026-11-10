# pandata

**The governed query layer for agents.**

Agents need to touch company data, and right now they do it with no guardrails. Pandata sits in the middle: it reads every question, checks who is really asking, fixes the question so it stays inside the rules, caps what it can spend, reuses safe answers, and says no when it should.

It is not another agent. It is the layer every agent has to go through, like Boat is for running code.

## Status

We are still checking if this is worth building. No code yet, no customers yet, no pilot yet.

Read `PROJECT.md` first. It is short, and it says plainly why this might work and why it might not.

## Docs

- `VISION.md`: the vision, ask your data a question and get a trusted answer in seconds
- `PROJECT.md`: what pandata is, who it is for, and how we test it before building it
- `COMPETITORS.md`: who else does this, and where the opening is
- `ELEVATOR_PITCH.md`: how to explain it in 15, 30, and 60 seconds
- `ROADMAP.md`: the gated plan: a one-day test, ten interviews, paid pilots, then build only if the tests pass

## The test, in one paragraph

First, a one-day test on real query logs: at least 20 percent of questions need to be reusable, and we need to add under 20 milliseconds in slow cases. Then ten interviews: at least 3 people need to say they will pay for a pilot. Then $500 a month pilots, measured on the customer's own bill, refunded if reuse is under 10 percent. If any of those fail, we stop.

## Who it is for

Companies with 50 to 500 people, running two or more database systems at once, Snowflake, BigQuery, Databricks, Postgres, ClickHouse, or DuckDB, with no single tool that covers them all. Not companies that use only one warehouse: their own tools already do that well.
