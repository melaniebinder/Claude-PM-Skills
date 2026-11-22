# Experiment Design

## What it does

Turns a feature or hypothesis into a pre-registered experiment plan. It builds the metric tree (one primary metric plus guardrails and diagnostics), picks the right randomization unit, computes sample size and duration with the math shown, lists the threats to validity, and sets ship, iterate, and kill decision rules before any data comes in. It also checks whether an A/B test is the right tool at all.

## When to use it

- You're about to launch a feature and need to prove its impact.
- You're reviewing a test plan and want to catch power, metric, or unit-of-randomization problems early.
- Stakeholders keep arguing about results after the fact, and you want the decision rules agreed up front.

## Example input

All data below is synthetic. This continues the fictional *Helply* example from the [AI Feature Spec](../ai-feature-spec/README.md) skill.

```
We want to A/B test Suggested Replies (LLM-drafted replies that agents can
edit and send). 1,200 agents across opted-in pilot accounts, organized in
~150 teams of ~8. Agents share macros and tips within their team.

Baseline: 5.0 tickets resolved per agent-hour (SD across agents: 1.5).
CSAT: 85% positive, ~15% of tickets get a survey response.
Leadership wants a decision within ~6 weeks. A 5% productivity lift would
justify the inference cost.
```

## Example output

> Abridged for length. The real output fills every section of the template.

---

# Suggested Replies: Experiment Design

**TL;DR:** A 4-week, team-randomized A/B test of Suggested Replies on 1,200 pilot agents. It is powered to detect a 5% lift in tickets resolved per agent-hour, with CSAT as the key guardrail. The result decides whether we ship to non-regulated tenants.

## 1. Hypothesis
If agents get a grounded draft reply for each ticket, then **tickets resolved per agent-hour** will increase by **≥ 5%**, because agents will spend less time composing answers to repetitive questions.

## 2. Metric tree
| Role | Metric | Baseline | Threshold |
|---|---|---|---|
| **Primary** | Tickets resolved per agent-hour | 5.0 | MDE: +5% (+0.25) |
| Guardrail | CSAT, % positive | 85% | Non-inferiority margin: −1.5pp |
| Guardrail | Ticket reopen rate (7-day) | 8% | ≤ +1.0pp |
| Guardrail | Escalation rate to Tier 2 | 12% | ≤ +1.0pp |
| Guardrail | Policy-violation rate (sampled audit) | n/a | ≤ 0.5% of sent drafts |
| Diagnostic | Draft acceptance rate, edit distance, p95 latency, fallback rate | n/a | Explains movement only |

## 3. Design
- **Randomization unit: team, not agent.** Agents share macros and tips within teams, so agent-level randomization would let treatment leak into control and shrink the measured effect.
- **Analysis unit:** agent, with cluster-robust standard errors at the team level.
- **Allocation:** 50/50, giving ~75 teams (600 agents) per arm. Ramp: 10% of treatment teams in days 1-2 (health check only), then 100%.
- **Triggering:** all agents in assigned teams who handle ≥ 1 ticket during the test.

## 4. Power and duration

**Agents needed per arm, ignoring clustering and variance reduction:**

n = 2 · (z₀.₉₇₅ + z₀.₈₀)² · σ² / δ² = 2 · (1.96 + 0.84)² · 1.5² / 0.25² ≈ **566**

**Adjustments:**
- **CUPED** using each agent's pre-period productivity (`ASSUMPTION:` ρ = 0.7): variance × (1 − 0.49), so n ≈ **289**.
- **Design effect** of team clustering (`ASSUMPTION:` ICC = 0.10, m = 8): 1 + 7 × 0.10 = 1.7, so n ≈ **491 per arm**.

**Feasibility:** 600 per arm are available, so the test is feasible, with about **87% power**. Without CUPED, it would need 962 per arm and could not run. That makes variance reduction a requirement, not a nice-to-have.

**Duration: 4 weeks.** That covers 4 full weekly cycles and lets us see whether gains hold after the novelty period. It also fits leadership's 6-week window with time for analysis.

**Guardrail power:** CSAT gets ~54K survey responses per arm. With team clustering (`ASSUMPTION:` ICC ≈ 0.01), the smallest detectable drop is ~1.5-1.7pp, so **this guardrail is borderline powered**. Mitigation: treat reopen rate as a co-guardrail, and extend to 6 weeks if CSAT trends negative at the week-3 health check.

## 5. Threats to validity
- **Sample ratio mismatch (SRM):** Run a χ² check on teams and agents daily. Pause if p < 0.001.
- **Novelty and learning:** Agents may over-use or under-use drafts at first. Report the weekly trend, and do not ship if the lift decays toward zero by week 4.
- **Interference:** Handled by team randomization. Monitor any cross-team agent transfers.
- **Peeking:** The primary analysis runs once, on day 28. Daily checks are for health and guardrails only.
- **Instrumentation:** Freeze the handle-time logging code for the duration of the test.

## 6. Decision rules (pre-registered)
| Outcome | Decision |
|---|---|
| Primary ≥ +5% and significant; all guardrails within margins | **Ship** to non-regulated tenants |
| Primary positive but < 5%, or not significant; acceptance ≥ 30% | **Iterate:** improve the retrieval for the 5 worst intents and re-test |
| Any guardrail breached | **Do not ship**, regardless of the primary metric |
| Primary significantly negative | **Kill** and write up the learnings |

**Decision owner:** Group PM for Agent Experience, with Data Science sign-off. **Analysis date:** day 28.

## 7. Pre-declared segments
Agent tenure (< 6 months vs. more), top-5 intents vs. long tail, account size, and ticket language (English vs. other). Any other cuts are exploratory only.

## 8. Readout template
> Suggested Replies changed tickets resolved per agent-hour by **[X% (95% CI)]**. Guardrails: CSAT **[Δ]**, reopen **[Δ]**, escalation **[Δ]**, all **[within / outside]** margins. Per the pre-registered rules, the decision is **[Ship / Iterate / Kill]**. Notable segment: **[...]**.
