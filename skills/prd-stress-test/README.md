# PRD Stress Test

## What it does

Reviews a PRD the way a skeptical leadership review would, before the review happens. It runs 8 lenses (problem, customer, metrics, engineering, data, legal and trust, design, finance), audits every load-bearing assumption, ranks the top issues by severity with one concrete fix each, predicts the hardest review questions, and rewrites the weakest sections so they're ready to paste back in.

## When to use it

- Your PRD goes to a leadership or architecture review next week.
- You're reviewing a teammate's PRD and want specific, useful feedback.
- A proposal feels thin but you can't say why.

## Example input

All data below is synthetic. It continues the fictional *Helply* story. This time a teammate proposes taking the human out of the loop, an option the [AI Feature Spec](../ai-feature-spec/README.md) deliberately rejected for launch.

```
PRD: Auto-Resolve for Password Resets (v0.3)

Problem: Password reset tickets are 18% of volume and are easy. Agents
shouldn't have to touch them.

Solution: When a ticket is classified as a password reset, the AI sends the
reset link and closes the ticket automatically. No agent involved.

Success: Fewer tickets for agents. Customers get faster responses.

Timeline: Ship in 4 weeks to all tenants.

Risks: Low. Password resets are simple and the classifier is 94% accurate.
```

## Example output

> Abridged for length. The real output includes the full assumption table and all rewrites.

---

# PRD Stress Test: Auto-Resolve for Password Resets

**Verdict:** Not ready
**One-line reason:** It proposes a fully autonomous, security-sensitive action to all tenants with no success metric, no misfire handling, and a risk section that calls a 6% error rate "low".

## What's strong (keep this)
- Targets a real, high-volume intent (18% of tickets). The opportunity is worth pursuing.
- The scope is narrow (one intent), which is the right shape for a first autonomous action.

## Top issues (ranked by severity)
1. **A 6% misclassification rate means automatic security actions on the wrong tickets** (Blocker, *Risks*). At 18% of ~4M tickets/month, a 94%-accurate classifier gives ~43K wrong decisions a month. Some of those will be account-takeover attempts that get a reset link without identity verification. **Fix:** Act only above a high-precision threshold (target ≥ 99.5% precision on a held-out set), require that the request comes from the verified account email, and route everything else to an agent.
2. **No success metric or guardrails** (Blocker, *Success*). "Fewer tickets" and "faster responses" have no baselines or targets. **Fix:** Primary metric: % of password-reset tickets resolved with no reopen within 7 days. Guardrails: account-takeover reports, CSAT on auto-resolved tickets, and reopen rate.
3. **Rollout to all tenants at once** (Blocker, *Timeline*). Regulated tenants (fintech, health) may contractually require a human to handle identity actions. **Fix:** Make it opt-in per tenant, starting with a non-regulated pilot. Get Legal review before any regulated tenant can enable it.
4. **No experience for misfires** (Major, *Solution*). The PRD doesn't say what happens when the customer's real issue wasn't a password reset and the ticket gets closed. **Fix:** Add a "This didn't solve my issue" link that reopens the ticket to the front of an agent's queue, and track its usage.
5. **No alternatives considered** (Major). Suggest-only mode (already live) or a self-serve reset link in the help widget might capture most of the value at lower risk. **Fix:** Add an alternatives section comparing all three.

## Assumption audit (excerpt)
| # | Assumption | Evidence | Risk if wrong | Cheapest test |
|---|---|---|---|---|
| 1 ⚠️ | The 94% accuracy holds on live traffic | Offline eval only | Wrong security actions at scale | Shadow mode for 2 weeks: classify but don't act |
| 2 ⚠️ | Password-reset requests are low-risk | None | Account-takeover vector | Security team threat model |
| 3 | Customers prefer instant auto-replies | None | CSAT drop | Compare CSAT on fast vs. slow resolutions in historical data |

## Questions you'll get in review
| Likely asker | Question | Answer? |
|---|---|---|
| Security | How do you stop an attacker from triggering resets for someone else's account? | No |
| Legal | Do any customer contracts require a human for identity actions? | No |
| VP Product | Why not just put a self-serve reset in the help widget? | No |
| Eng lead | What's the precision at the threshold you'll actually use? | Partial |
| Finance | How many agent hours does this save beyond Suggested Replies? | No |

## Rewrites

**Success metrics (rewritten):**
> **Primary:** % of password-reset tickets auto-resolved with no reopen within 7 days. Baseline: 0%. Target: ≥ 60% of password-reset volume in pilot tenants.
> **Guardrails:** Account-takeover reports per 10K resets (no increase vs. baseline); CSAT on auto-resolved tickets ≥ the agent-handled baseline (91%); reopen rate ≤ 5%.
> **Diagnostics:** Classifier precision at the action threshold, % routed to an agent, and "didn't solve my issue" click rate.

## Action items before review
- [ ] Get a threat model from Security (it blocks everything else).
- [ ] Run the classifier in shadow mode for 2 weeks and report precision at the action threshold.
- [ ] Add success metrics and guardrails (rewrite above).
- [ ] Change the rollout to opt-in per tenant, starting with non-regulated tenants.
- [ ] Add an alternatives section: suggest-only vs. self-serve vs. auto-resolve.
