# Iterating PanData: where is the value, really?

Date: 2026-11-10. This is an iteration on `PROJECT.md`, `VISION.md`, and the Nov 6, 2026 market research. It does not replace them. It corrects them where they were wrong, and narrows them where they were too broad.

First, a correction. The score in `PROJECT.md` and `VISION.md` says 29 out of 45. That is wrong. The line items in the Nov 6 scorecard add up to 24 out of 45, not 29. Pass is 31. So the original idea is further below the bar than we said. Use 24 from now on.

## What we got wrong in the first version

The first version priced PanData on saving money on the warehouse bill. That is the wrong value, for most customers.

A typical target company spends about $24,000 a year on its warehouse. Even if we reuse 20 percent of agent questions, it saves about $5,800 a year. That is less than a $12,000 a year price. ROI 0.48x. It does not pay for itself.

That does not mean there is no value. It means we were measuring the wrong value. Saving money is the smallest, hardest-to-prove value. The bigger values are below, and they need a different test for each one.

## Where value could actually live

We looked again, at the same research, plus fresh checks on 10 Nov 2026. Six places value could live, ranked by how much value, how easy to build, and how easy to sell.

### 1. Speed, not savings: the cache alone

**The idea:** Do not build all five functions. Build one: safe reuse of answers, with permissions as part of the lookup. An agent asks a question, PanData checks if a safe answer already exists, and returns it in under a second, without running the warehouse or the model again.

**Why it could be valuable:** Agents run 50 to 200 database queries per task, and 70 to 85 percent of those queries ask for almost the same data, in slightly different ways. That claim comes from a DZone piece, Dec 2025, not from our own measurement, so treat it as a claim to test, not a fact. But if it is even half true at scale, the value is not in a $24,000 a year company. It is in a company spending $150,000 to $300,000 a month on warehouse costs at agent scale, where a cache is worth $80,000 to $200,000 a month in that same piece, again a claim, not a measurement.

Our own Nov 6 scenarios used 10 to 30 percent reuse, and even then the base case failed. The cache alone only works if we target high-spend agent estates first, not the mid-market base case. That is a different customer: bigger warehouse bill, more agents, more repeated questions.

**How to build it:** Smallest slice. A proxy that normalises SQL, checks permissions, and serves a cached answer. No semantic layer, no full policy engine, no dashboard. Rust, in path, under 20 milliseconds in slow cases.

**How to sell it:** As an add-on, not a platform. $200 to $500 a month, or a share of measured saving, with a refund if reuse is under 10 percent. It plugs into a gateway or warehouse the customer already has.

**Test, in one day:** On 1,000 real consecutive queries, what is the normalised reuse rate? Pass at 20 percent or more, kill under 10 percent. And: what is the warehouse spend of the firms we test on? If it is under $25,000 a year, this pivot fails on price, even if the reuse rate passes.

**Rank: 1st. Best value per effort, easiest to measure, easiest to kill.**

### 2. Unlocking, not saving: let blocked teams use agents at all

**The idea:** Many teams do not use agents on real data at all, because security blocked it. The value is not saving $5,800. It is enabling work that does not happen today: a product manager asking a question without waiting for an analyst, a finance lead checking a number herself, a support lead finding an answer in seconds.

**Why it could be valuable:** Time is worth more than warehouse cost. If an analyst costs $150,000 a year, and spends half her time answering the same 20 questions, unlocking that time is worth far more than $5,800. Faster decisions are worth more than a smaller bill. But this value is hard to measure, and easy to overclaim. It only counts if the customer will pay for it, not if they say it sounds nice.

**How to build it:** Same as the full layer, but sell the outcome, not the saving. "Your team can ask data questions, safely, today." The product is the same five functions, but the pitch and the price change.

**How to sell it:** Price on time and on access, not on warehouse saving. Ask in interviews: "What would you pay to unblock agents for your team?" Not: "What would you save on your bill?" If the answer is under $500 a month, this pivot fails.

**Test, in ten interviews:** At least 3 data team leads commit to a $500 a month pilot, on the basis of unblocking, not saving. A "sounds interesting" does not count.

**Rank: 2nd. Biggest value if true, hardest to prove, and the test is just talking to people.**

### 3. Fast answers for people who ask every morning: the Cortana slice

**The idea:** PanData as the fast layer for the 20 questions a company asks every day. Pre-compute them overnight, reuse them safely in under a second, and only use the model and the warehouse for new questions. This is the vision in `VISION.md`, narrowed to the daily questions.

**Why it could be valuable:** Speed is felt every day, saving is felt once a month on a bill. A product lead who gets yesterday's numbers in under a second, every morning, will notice. A $60 accidental scan that never runs will not be noticed. Speed is also easier to demo: ask, answer, under a second, in front of the customer.

**How to build it:** Pre-aggregations plus the cache from pivot 1, plus a small list of definitions for those 20 questions. No need to cover every possible question. Cover the daily ones well, and say no to the rest.

**How to sell it:** Price on time to answer, not on warehouse saving. "Under a second, for your daily questions, with the definitions shown next to the number."

**Test, in one day, same as pivot 1, plus:** How many of the top 20 daily questions are repeated, in real logs? If the top 20 cover less than half of all questions, this pivot is too narrow.

**Rank: 3rd, tied to pivot 1. It is pivot 1, sold as speed instead of saving. Test them together.**

### 4. Audit and consent for regulated firms in Chile

**The idea:** In Chile, banks and fintechs have to share data with consent, and prove who saw what, when, and why. Law 21.719 is in full force from 1 Dec 2026. Open Finance, NCG 514, goes live in July 2027, after a 36-month rollout, with consent, traceability, and audit as requirements, not nice-to-haves. PanData as the audit trail for agent access to financial data.

**Why it could be valuable:** Compliance is not priced on saving. It is priced on avoiding a fine, or on being allowed to operate at all. A bank will pay $12,000 to $60,000 a year for audit evidence, if it needs it to pass a check. That is a higher price than the mid-market, for a smaller market: 19 banks plus about 40 authorised fintechs, about 60 to 120 institutions in total, about $2 to $3 million a year in total, central estimate. Small, but high willingness, if the deadline is real.

**Why it could fail:** The market is tiny, sales are slow, and banks buy from vendors they already trust, not from a solo builder. The rollout is phased over five years, so the urgency is less than the headline date suggests. And a postponement bill was already in the Senate in Oct 2026.

**How to build it:** The log first, not the cache. Every decision, rewrite, cap, reuse, and refusal, with consent attached, immutable, and exportable for a regulator. That is a different product emphasis from pivots 1 to 3.

**How to sell it:** To a compliance lead, not a data lead. Price on audit evidence, not on saving.

**Test, in five interviews in Chile:** Would a compliance lead pay $1,000 a month or more for an agent audit trail, and would they sign a pilot before July 2027? If not, this pivot is a beachhead, not a business.

**Rank: 4th. Highest price per customer, smallest market, slowest sale.**

### 5. Sell it inside other products, not direct

**The idea:** Do not sell PanData to companies. Sell the cache and cap library to vendors who already have those companies as customers: ClickHouse, MotherDuck, Cube, dbt Labs, Starburst. They embed it, they sell it, PanData gets a licence fee.

**Why it could be valuable:** No sales team needed, no onboarding per customer, and the vendor already has trust. A rough deal: $50,000 to $250,000 a year, plus a small amount per question, or 10 to 20 percent of what they charge for it. One deal is worth 10 to 40 direct customers.

**Why it could fail:** It takes 6 to 12 months to close, and vendors only buy a component they cannot build themselves in a quarter. Dremio or Immuta could build a cache in that time. And a vendor will want it proven on real customers first, which brings us back to pivots 1 and 2.

**Test:** After the spike and interviews, not before. One conversation with ClickHouse or MotherDuck, asking: "Would you embed a cache and cap layer, and what would you pay for it?" If no second meeting after five tries, stop.

**Rank: 5th. Best leverage if it works, slowest, and it depends on the other pivots working first.**

### 6. Personal PanData, for yourself

**The idea:** Build the fast, governed layer for your own data, not for a market. Twenty questions you actually ask, definitions written once, pre-computed into DuckDB hourly, answers reused safely, with a chat or voice front end. No sales, no customers, no price.

**Why it is valuable:** It is guaranteed to be useful to one person, you, and it is fun to build, in Rust, in a weekend or two. It is also the fastest way to learn if the cache idea actually works, on real logs, without waiting for a customer.

**Why it is not a company:** It does not need to be. A side project does not need 200 customers. It needs to work for you.

**Test:** Does it answer your daily questions in under a second, and do you actually use it for a week? If yes, it is a success as a side project, even if it never becomes a company.

**Rank: 1st as a side project, last as a company. Do this first if you want to build something, not sell something.**

## What changed from the first version

| Before | After |
|---|---|
| Sell on saving money | Sell on speed, on unlocking blocked teams, or on audit evidence. Saving alone fails at 0.48x ROI. |
| Target: any 50 to 500 person firm, $24k a year spend | Target first: high-spend agent estates for the cache, or regulated firms for audit, or yourself for the side project. The base $24k firm is the worst customer. |
| Build all five functions | Build one first: the cache, with caps. Add the rest only if the cache proves itself. |
| Price: $500 to $2,500 a month, tiered by warehouse spend | Price: $200 to $500 a month for the cache add-on, $500 a month for a pilot, $12k to $60k a year only for regulated audit, and only if a compliance lead signs it. |
| Score: 29 out of 45 | Score: 24 out of 45, corrected. Further below the 31 bar. |
| Vision: a full governed query layer | Vision, narrowed: a fast, trusted answer in seconds, for the questions you actually ask every day, with a clear no when you should not see it. |

## The ranked recommendation

**Do this, in order, and stop at the first failure:**

1. **Build the personal version first, for a week.** Twenty questions, DuckDB, definitions, cache. If you do not use it, kill the idea. If you do, you have real logs to test the cache on.
2. **Run the one-day spike on those logs, plus any public logs.** Pass at 20 percent reuse, kill under 10 percent. And check the spend: if the firms you would sell to spend under $25,000 a year, the cache pivot fails on price, even if reuse passes.
3. **Talk to ten people, with the new question.** Not "what would you save?" but "what would you pay to unblock agents, or to get daily answers in under a second?" At least 3 commit to a $500 a month pilot, or stop.
4. **Only then, talk to five Chile compliance leads,** about audit, and to ClickHouse or MotherDuck, about embedding. Those are slow, so start them last, not first.

**Do not do this:** Do not build the full five-function platform as a company. Do not target the base $24,000 a year firm. Do not price on saving alone. Those three were the first version, and they are the reason the score is 24, not 31.

## What would change this ranking

- If the spike shows reuse over 50 percent on high-spend logs, pivot 1 moves from "test" to "build the cache now."
- If 3 or more data leads say they will pay $1,000 a month or more to unblock agents, pivot 2 moves to first.
- If a Chile bank signs a pilot for audit before July 2027, pivot 4 moves to first, and the market is smaller but real.
- If Dremio or Immuta ship a safe cache, pivot 1 dies, and only pivots 4 and 6 remain.
- If you do not use the personal version for a week, kill all of it. A product you do not use yourself is not a side project, it is a chore.

## Sources

- Nov 6, 2026 market research, sections 1, 4, 5, and 6. Score correction: line items sum to 24, not 29. ROI correction: 0.48x, not 0.6x, on the detailed agent-share method.
- Nov 9, 2026 three-idea review: 2 out of 5 as a standalone company, narrow wedge only.
- Fresh checks, 10 Nov 2026: DZone, Dec 2025, on semantic caching for agents, 70 to 85 percent of queries asking for almost the same data, $150,000 to $300,000 a month warehouse costs at scale, claims not measurements. Strategy Mosaic, vendor claims, 551 percent ROI, $3.4 million average net impact, claims not measurements. Chile Open Finance, NCG 514, entry July 2027, 36-month rollout, consent and audit requirements, via CMF and Carey, 2026.
- `PROJECT.md`, `VISION.md`, `COMPETITORS.md`, `ROADMAP.md` in this repo, as of 10 Nov 2026.
