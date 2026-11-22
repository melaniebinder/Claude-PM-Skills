# Claude PM Skills

A collection of Claude skills I built to make product management work faster and more rigorous.

## Skills

| Skill | What it does | Use it when |
|-------|--------------|-------------|
| [ai-feature-spec](skills/ai-feature-spec) | Turns an AI feature idea into a decision-ready spec: model choice, eval plan, failure modes, guardrails, and cost | You're scoping, pitching, or reviewing any LLM-powered feature |
| [experiment-design](skills/experiment-design) | Builds a pre-registered A/B test plan: metric tree, randomization unit, power math, and decision rules | You need to prove a launch's impact, or catch a flawed test plan before it runs |

## How to use

1. Open the skill's folder and copy `SKILL.md`
2. Add it to Claude as a skill, or paste it into a chat or Project
3. See the skill's README for an example input and output

## Repo structure

```
claude-pm-skills/
├── README.md
├── skills/
│   └── <skill-name>/
│       ├── SKILL.md      # the skill itself
│       └── README.md     # what it does + example
└── examples/             # sample inputs/outputs (synthetic data)
```

## Why I built these

AI features are easy to demo and hard to ship well. The gaps are almost always the same: no eval plan, no answer for when the model is wrong, cost math nobody did, and experiments argued about after the fact. I wanted Claude to apply the same Principal-level rigor every time, so every spec and test plan starts from a high bar instead of a blank page.

*Built on my own time. No proprietary or employer data.*
