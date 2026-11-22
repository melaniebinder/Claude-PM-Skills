# Design Taste Sparring

## What it does

A sparring partner for sharpening product design taste, a skill PMs are increasingly expected to have as they prototype with AI tools and make design calls directly.

- **Critique mode:** You share a design. It makes you commit to your own judgment first, then challenges it: ranked issues with a named principle for each, a steelman of the current design, a pared-down version, and two directions for you to choose between. It ends with a scorecard rating **your reasoning**, not the design.
- **Drill mode:** It gives you realistic design dilemmas one at a time. You pick and justify. It scores your reasoning, explains when the option you rejected would actually win, and after a few rounds names the pattern in how you think.

It includes extra lenses for AI-native UX: designing for review rather than blind acceptance, honest uncertainty, editability, how latency feels, and capturing feedback from natural behavior.

## When to use it

- Before a design review, to stress-test a mockup or flow.
- When reviewing AI-generated UI or a prototype you built yourself.
- When you want to build taste deliberately, for 15 minutes at a time.

## Example input

All data below is synthetic. It continues the fictional *Helply* story with the composer UI for [Suggested Replies](../ai-feature-spec/README.md).

```
Critique mode. Here's the current design for Suggested Replies in the agent
composer:

- When a ticket opens, the AI draft is inserted directly into the reply box.
- A yellow banner above it: "⚠️ AI-generated content. Please review before
  sending."
- A badge in the corner: "Confidence: 87%"
- Three buttons under the box: [Accept] [Regenerate] [Rate 👍👎]
- Send button is in its usual place.

My take: core job is "help agents reply faster." Verdict: ship, it's clean.
Biggest problem: agents might want different tones, so I'd add a tone
selector (friendly / formal / concise).
```

## Example output

> Abridged for length.

---

# Design Sparring: Suggested Replies Composer

## Your call vs. mine
| | You | Me |
|---|---|---|
| Core job | Help agents reply faster | Help agents send a **correct** reply faster |
| Verdict | Ship | Iterate |
| Biggest problem | No tone options | The design invites sending without reading |

**Where we differ and why:** You framed the job as speed, so the fix you reached for was more output options. But this feature's real risk is an agent sending a wrong refund policy that they never read. When the core job includes "correct," the design has to make review effortless, not just make acceptance easy. Adding a tone selector is a classic taste trap: it adds a control instead of fixing the default.

## Critique (ranked by impact)

**1. A draft inserted into the reply box looks finished.** Lens: Trust and control. Principle: *Friction should match risk.*
Because it sits in the box looking like a complete reply, agents will send it as-is, and the "Accept" button barely matters. Combined with the warning banner, you get blind acceptance with a legal disclaimer on top.
**Steelman:** Inserting the draft into the box is the fastest path, and the experiment measures speed.
**Better:** Show the draft as a distinct suggestion with **inline citations** on the policy claims (e.g. "refund within 30 days [Billing Policy §2]"). One click or Tab puts it into the box. The extra step is tiny, but it's a moment of review exactly where the risk is.

**2. "Confidence: 87%" is false precision.** Lens: AI UX, honest uncertainty. Principle: *Only show a number if it changes what the user does.*
Agents can't act differently on 87% vs. 91%. A number will either be ignored or trusted too much.
**Steelman:** It signals transparency to buyers and compliance teams.
**Better:** Show nothing when the draft is well-grounded. When it isn't, change what the user sees: "No help center source found for this. Check before sending," with the unsupported sentence highlighted.

**3. The banner is boilerplate.** Lens: Words. Principle: *A warning on everything is a warning on nothing.*
Agents will stop seeing it by day two. Delete it, and let the citations and the targeted low-confidence state above do the work.

**4. The "Rate 👍👎" button will collect almost no data.** Lens: Subtraction. Principle: *Learn from behavior, not from buttons.*
The edits agents make to the draft are far better feedback. Remove the button and log edit distance and edited spans.

**5. The tone selector (your fix).** Lens: Defaults. Principle: *Fix the default before adding a choice.*
Learn each tenant's tone from their own resolved tickets. Add a selector only if edit data shows agents keep rewriting for tone.

## The subtraction version
The ticket on the left. On the right, a suggestion card: the draft, with small citation chips on the factual claims. Press Tab to use it, then edit in place. If the draft isn't grounded, the card says so in one line and highlights the unsupported sentence. No banner, no score, no rating, no tone menu.

## Two directions
| | A: Suggestion card + Tab to use | B: Inline ghost text (like code autocomplete) |
|---|---|---|
| Idea | A separate card with citations; one key to use it | Gray text in the box; Tab to accept piece by piece |
| Optimizes for | Review of policy claims | Speed and flow for frequent short replies |
| Gives up | One extra keystroke | Citations are harder to show; easier to accept blindly |

**Your move:** Which would you pick for Helply, and why?
*My recommendation: A for launch, given that refunds and policies are involved. B might win later for low-risk intents like shipping status, once edit data shows those drafts are reliably sent unchanged.*

## Taste scorecard
| Dimension | Score | Note |
|---|---|---|
| Spotted the core job | 3 | Got speed; missed correctness |
| Saw what to remove | 1 | Proposed adding, not removing |
| Judged risk vs. friction | 2 | Didn't connect auto-insert to over-trust |
| Thought beyond the happy path | 2 | No low-confidence or no-source state |
| Explained with principles, not preference | 3 | Tone idea grounded in a real user need |

## Principle to keep
**When an AI output can be wrong in ways that matter, design for review, not for acceptance, and fix the default before you add a choice.**
