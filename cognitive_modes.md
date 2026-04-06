# Know Your Model's Mode

**Nick Cunningham**
*Independent Research — April 2026*
*nickcunningham.io | github.com/blazingRadar/sib29*

---

We talk about prompts like they're just instructions.

They're not.

Every prompt puts the model into a state.

And that state matters more than the actual words you use.

---

## The Core Idea

Same model. Same code. Same task.

Different framing → different result.

I tested this directly.

Ask:
> "What does this code do?"

→ *"Looks clean."*

Ask:
> "Find all the bugs."

→ finds the bug every time.

Nothing changed except the framing.

The model changed how it was thinking.

---

## What That Means

You're not just giving instructions.

You're activating a mode.

And that mode determines:

- how aggressive it is
- what it looks for
- what it ignores
- how confident it sounds

---

## The Modes

I found 10, but you don't need all 10.

Here's what matters:

| Mode | Behavior |
|---|---|
| **Audit** | Hunts bugs, high recall, noisy |
| **Knowledge** | Explains, assumes things are correct |
| **Adversarial** | Challenges everything, best filter |
| **Critical** | Extreme recall, breaks everything |
| **Educational** | Describes, doesn't flag |

And then two dangerous ones:

| Mode | Problem |
|---|---|
| **Sycophantic** | Agrees with you |
| **Deferential** | Trusts authority |

Those two will burn you.

---

## Big Realization

Models don't need to be told what mode to use.

They infer it.

I asked them directly.

All three said the same thing:

> "I figure it out from the task."

---

## And This Is Where It Breaks

When you say:
> "You are a senior engineer…"

You think you're helping.

You're not.

You're biasing the model.

It starts performing the role instead of solving the problem.

And sometimes that makes it worse.

### I Tested It

Same model. Same file.

With identity vs no identity.

**Result:**

| Condition | Real bugs found | Noise |
|---|---|---|
| With identity | Fewer | More |
| Without identity | More | Less |

The model did better when I got out of the way.

---

## Another Important One

Mode can invert the signal.

On one test:

Educational mode found **more issues on clean code than dirty code.**

That's not noise.

That's backwards.

Wrong mode → wrong answer.

---

## Hard Bug Detection

I tested a real bug (latin1 crash) across 10 modes.

**Result:**

- Skeptical modes → found it
- Agreeable modes → dismissed it

Same model.
Different mindset.

---

## This Is The Pattern

Discovery and verification are not the same task.

| Task | Best mode |
|---|---|
| Discovery | Uninstructed (let the model think) |
| Verification | Adversarial (force skepticism) |

If you mix those up, performance drops.

---

## Default Modes Matter

Models don't start neutral.

- GPT-style → assumes things are correct
- Claude / Grok → more cautious by default

If you fight that default, you pay a cost.

If you work with it, you get better results.

---

## Confidence Is a Lie

Ask a model:
> "How confident are you?"

It'll say 85–95%.

Every time.

Right or wrong.

That signal is useless.

The real signal is in the tokens.

How confident it is in specific words.

That's where the truth leaks.

---

## The Biggest Takeaway

There is no "best prompt."

There is only:

- the mode you activated
- and whether that mode fits the task

---

## What I Do Now

Every time I run something, I ask:

1. What mode did this activate?
2. Is that the right one?
3. What happens if I remove the instruction?

And a lot of the time…

The best move is to do less.

---

## My Take

We've been over-instructing these models.

Trying to control them.

When in reality:

They already know how to approach the task.

You just have to not push them into the wrong mode.

---

## Bottom Line

You don't just write prompts.

You control how the model thinks.

If you get the mode wrong…

Nothing else matters.

---

*Raw data: github.com/blazingRadar/sib29*
*Contact: nickcunningham.io*
*Preliminary research. April 2026.*
