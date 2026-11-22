# Exec Narrative

## What it does

Turns messy notes, data, and context into a crisp executive decision document in the style of a 6-pager memo. It puts the ask first, states tenets a reasonable person could disagree with, gives honest evidence, compares real options in a table, makes a recommendation without hedging and names its tradeoff, and ends with an FAQ that includes the question you least want to be asked.

## When to use it

- You need a leadership decision, funding, or headcount, and you have one meeting to get it.
- You have results and notes scattered across docs and Slack, and need one clear story.
- Slides would hide the reasoning, and the decision deserves a written argument.

## Example input

All data below is synthetic. This is the final chapter of the fictional *Helply* story: the [experiment](../experiment-design/README.md) finished, and now leadership has to decide what to do next.

```
notes for the SR decision doc, exec staff next thurs

- test done! 4 wks, 1,200 agents, team randomized
- tickets/agent-hr +7.2% (CI +4.1 to +10.3), above our 5% bar
- CSAT -0.4pp (CI -1.3 to +0.5), inside the -1.5pp margin
- reopen +0.2pp, escalations -0.5pp, no policy violations in audit
- acceptance 41%, way higher for billing/shipping than for tech issues
- new agents (<6mo) got +11%, tenured +5%
- want: GA to non-regulated now + 2 eng and 1 compliance person for regulated (fintech/health = ~30% of agents, and they're asking for it)
- sales thinks they can charge $30/agent/mo as an add-on
- someone on exec will ask about auto-resolve (that PRD got sent back)
- platform is ~5,000 agents total, inference ~$240K/yr if everyone used it
```

## Example output

> Abridged for length. The real output includes the full FAQ and appendix.

---

# Launch Suggested Replies to All Non-Regulated Tenants Now, and Fund Regulated Readiness for Q2

## The ask
We're asking for approval to launch Suggested Replies to all non-regulated tenants this month, and for 3 people (2 engineers, 1 compliance lead) to make it available to regulated tenants by the end of Q2. We recommend both. The experiment beat our pre-registered bar with no guardrail breaches. Regulated tenants make up ~30% of agents, are asking for the feature, and are the segment most at risk of churning to competitors that already offer AI.

## Context
Agents at our customers spend most of each ticket writing replies to repetitive questions. Suggested Replies drafts a grounded reply that the agent reviews and sends. The agent stays in control of every message. We tested it in a pre-registered experiment before making any launch decision.

## Tenets
1. **We earn autonomy.** AI suggests before it acts, and it acts only where we've proven it's right. This is why we are not proposing auto-resolve.
2. **Customer trust beats speed.** We will delay a segment's launch rather than ship something we cannot defend to their compliance team.
3. **We price on the value we create, not on our cost.**

## What we learned
Suggested Replies increased tickets resolved per agent-hour by **7.2%** (95% CI: +4.1% to +10.3%). That clears our pre-registered 5% bar. Every guardrail held: CSAT moved −0.4pp (inside our −1.5pp margin), reopens rose by 0.2pp, escalations fell by 0.5pp, and an audit found no policy violations.

Two findings shape the rollout. First, the benefit is uneven. Agents with less than 6 months' tenure gained 11%, versus 5% for tenured agents. That makes this an onboarding story as much as a productivity story. Second, drafts were accepted 41% of the time overall, but much less often for technical issues than for billing and shipping. That points to where retrieval needs work next.

What is still uncertain: the CSAT confidence interval reaches −1.3pp. That is inside our margin, but not by much, so we will keep monitoring CSAT after launch.

## Options
| | A: Non-regulated only | B: Non-regulated + fund regulated (recommended) | C: Hold and build auto-resolve |
|---|---|---|---|
| What it is | GA to ~3,500 agents | A, plus 3 people for Q2 readiness | Delay GA and invest in autonomy |
| Impact | ~250 FTEs of customer capacity | ~360 FTEs at full rollout | Unproven; PRD sent back in review |
| Revenue (40% attach, $30/agent/mo) | ~$500K ARR | ~$720K ARR | $0 this year |
| Cost | Inference ~$70K/yr | Inference ~$100K/yr + 3 people | Team time; delays proven value |
| Risk | Churn risk in regulated tenants | Compliance execution | Security risk and trust |
| Reversible? | Yes (feature flag) | Yes | Partially |

## Recommendation
Option B. It captures the proven value now and closes the gap with the customers who are most at risk. Under our tenets, Option C is premature: it skips the "earn autonomy" step that this experiment just started. The tradeoff we accept is that the 3 people won't work on the next AI feature this half.

## Risks and mitigations
- **CSAT drifts down after launch** (owner: PM). Weekly monitoring, with automatic rollback for any tenant that drops more than 1.5pp.
- **Regulated readiness slips** (owner: Eng manager). Scope is fixed to audit logging, data residency, and opt-out controls.
- **Low acceptance on technical tickets limits value** (owner: Applied Science). A retrieval improvement sprint targeting the 5 worst intents.

## FAQ
**Why not auto-resolve, since it would save even more?**
That PRD was sent back for good reasons: a 6% misclassification rate on a security-sensitive action, with no misfire handling. We'll revisit it once we have 90 days of production acceptance and edit data by intent.

**Is $30/agent/month the right price?**
It captures about 4% of the ~$18M/year of capacity we create for customers at full rollout. That leaves clear value on the table for them. Sales will validate it with 10 pilot accounts before list pricing is set.

**What if a regulated customer finds a compliance issue?**
Regulated tenants are opt-in only, behind Legal sign-off and an audit-log review. Any finding pauses that segment.
