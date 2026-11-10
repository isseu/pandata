# The vision for PanData

**Ask your data a question, in plain words, and get a trusted answer in seconds. Not in minutes. Not next week. Not from a dashboard someone built for a different question.**

That is the whole vision. Everything else in this repo is how we test if it is real, and how we would build it if it is.

Status: this is a vision, not a product. No code yet, no customers yet, no pilot yet. The numbers below are targets to prove, not results we have.

## The world today

Today, data is slow in three ways.

First, it is slow to ask. If your question is not already in a dashboard, you write SQL, or you wait for someone who can. Most people in a company cannot ask the data a new question at all.

Second, it is slow to answer. An agent can write the SQL for you now, but it takes 10 to 30 seconds, it explores the schema, it tries, it fails, it tries again, and then the warehouse scans a huge amount of data. You wait, and you pay for the waiting.

Third, it is slow to trust. When the answer finally comes, you do not know if it is right. Did it use the right definition of revenue? Did it see data it should not have seen? Did it cost $60 to answer a $1 question? Most companies answer those questions by blocking agents from real data altogether.

So data is powerful, and most of the company cannot use it, quickly or safely.

## The world with PanData

You ask, in plain words, out loud or in chat:

"How did sales do yesterday, compared to last week, in Chile?"

"Why did churn go up this month?"

"Which customers are at risk, and what changed for them?"

And you get an answer in seconds, with the number, where it came from, and what it cost. If the question is outside what is defined, or you do not have permission to see it, you get a clear no, with a reason, not a guessed number.

That is what Cortana promised, and never delivered for data: a personal assistant that knows your data, answers fast, and does not make things up.

PanData is not Cortana. It is the layer underneath that makes a Cortana for data possible, and safe.

## What it feels like

Morning, 9am. A product lead opens her laptop and asks how yesterday went. The answer is already there, because the common questions were computed overnight, and the answer is reused safely, in under a second.

Midday, a finance lead asks a new question, one nobody has asked before. It takes a few seconds, not a ticket to the data team. The question runs, the cost is capped, the answer is logged, and the definitions it used are shown next to the number.

Afternoon, a support lead asks a question she is not allowed to see the answer to. She gets a no, in plain words, and a suggestion for the question she is allowed to ask. No leak, no awkward conversation, no ticket to security.

Nobody in that story wrote SQL. Nobody built a dashboard for that question. Nobody waited for an analyst. And nobody in security had to say no to the whole idea, because the rules were in the question itself.

## Three words: fast, right, safe

The vision rests on three things, and all three have to be true at the same time. If one is missing, the vision fails.

**Fast.** Most questions are the same 20 questions, asked again and again. They should be answered in under a second, from a safe reused answer, or from a pre-computed table. New questions should take seconds, not minutes. The model should only run when the question is actually new. The warehouse should only run when the answer is not already known.

**Right.** Fast and wrong is worse than slow. An answer is right because the business definitions were written down once, by a person, and the layer enforces them. "Revenue" means the same thing every time, for everyone. If a question is outside those definitions, the answer is no, not a guess. A wrong number, delivered quickly, is still a wrong number.

**Safe.** Fast and right, but leaking data, is not a product. Every question is checked against the real person asking, not just the agent. Columns are hidden, rows are filtered, costs are capped before anything runs, and every decision is logged. The log is not a report after the fact. It is the proof, in the moment, that the answer was allowed.

Claude alone can be fast, sometimes, and smart, sometimes. It cannot be safe and right on its own, reliably, because its rules are in a prompt, and prompts can be ignored. PanData alone cannot be smart at all. Together, Claude does the thinking, PanData makes it fast, right, and safe enough to ship.

## What PanData is, in this vision

- The layer every agent has to go through to touch company data, across Snowflake, BigQuery, Databricks, Postgres, ClickHouse, and DuckDB, not just one of them.
- The place where the business definitions live, and where they are enforced, not just written in a document.
- The place where common answers are reused safely, so the second person asking the same question gets it in a second, not in 30 seconds, and not at full price.
- The place where cost is capped before it is spent, and where every answer can be traced back to who asked, what they saw, and what it cost.
- Invisible. You should not have to think about it. You ask, you get an answer. That is the Boat model: cheap, fast, invisible, measurable, and easy to embed in other products.

## What PanData is not, in this vision

- Not another chatbot. It does not chat. It checks questions.
- Not another dashboard. Dashboards answer the questions someone guessed in advance. PanData answers the question you actually asked.
- Not a warehouse. It does not store your data. It sits in front of the warehouses you already have.
- Not a model. It does not get smarter over time on its own. It gets faster and more trusted as the definitions get better, and as more answers are safely reused.
- Not a company yet. This vision is worth testing, not worth assuming. Our research scored it 29 out of 45, below our bar of 31, and 2 out of 5 as a standalone company. The vision is the reason to test it. The test is the reason to believe it, or kill it.

## How we get there, in stages

The vision is big. The path is small, and gated. See `ROADMAP.md` for the full plan.

1. **Prove it is fast.** A one-day test on real query logs. At least 20 percent of questions reusable, and under 20 milliseconds added in slow cases. If not, stop.
2. **Prove someone will pay for it.** Ten interviews. At least 3 will pay for a pilot, on the basis of their own bill. If not, stop.
3. **Prove it on real bills.** Paid pilots at $500 a month, measured on the customer's own logs, refunded if reuse is under 10 percent. If not, stop.
4. **Build the first version.** Only if the pilots pass. Snowflake and Postgres first, in Rust, inside the customer's own systems.
5. **Make it invisible.** Onboarding under 2 weeks, including writing the definitions, or a vendor deal to embed it in another product. Only one of those, not both at once.

Each stage has a number, and a stop point. A vision without a stop point is just a pitch.

## What success looks like

In five years, if this works, asking data a question feels like asking a person who knows the answer, and you trust them.

- A question you ask every morning takes under a second.
- A new question takes seconds, and you can see the definitions, the cost, and the log next to the answer.
- A question you should not see gets a clear no, not a leak, and not a lecture.
- A data team spends its time writing definitions and improving answers, not answering the same 20 questions, and not saying no to agents.
- A company with 50 to 500 people, running several database systems, can give every team a data assistant, without hiring a data team for every team.

That is the vision. Not a faster dashboard. Not a smarter chatbot. A trusted answer, in seconds, for anyone who is allowed to ask.

## One line to remember

**PanData makes asking your data feel instant, and makes the instant answer one you can trust.**

If we cannot prove fast, right, and safe, all three, on real logs and real bills, then the vision is wrong, and we stop. That is in `PROJECT.md`, in `ROADMAP.md`, and it is in this file on purpose.
