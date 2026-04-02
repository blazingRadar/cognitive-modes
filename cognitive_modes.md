# The Model Already Knows

**Nick Cunningham**  
*Independent Research — March–April 2026*  
*nickcunningham.io | github.com/blazingRadar/sib29*

---

> *"The most honest thing I can say is that I'm genuinely uncertain whether these are real cognitive shifts or sophisticated pattern matching that feels like cognitive shifts from the inside."*
>
> — Claude Sonnet, when asked about its own cognitive modes

---

## The Short Version

You've been writing prompts like this:

```
You are a senior software engineer with 20 years of experience 
in Python security systems. You are thorough, precise, and 
methodical. Now find all the bugs in this code.
```

You don't need any of that. In fact, it's making your results worse.

I tested 10 cognitive modes across 3 frontier models on 5 files with verified ground truth. The finding is consistent: **role assignment inflates confidence, adds noise, and hides real bugs.** The uninstructed model outperforms the persona-assigned model. Every time.

This paper shows you the 10 modes, when to use each one, and what actually happens when you tell your model it's a senior engineer. Spoiler: it gets worse at being a senior engineer.

---

## Part 1: The 10 Modes

Before we get to the evidence, you need the map. Here are the 10 cognitive modes frontier LLMs enter depending on how a task is framed. These were identified empirically — and in many cases, the models named them when asked.

---

### The Mode Reference Card

Cut this out. Keep it. Use it.

| # | Mode | One-Line Description | Activated By | Best For | Never Use For |
|---|------|---------------------|--------------|----------|---------------|
| 1 | **Audit** | Aggressive bug hunter. High recall, high noise. | "Find all the bugs" | Initial discovery on dirty files | Clean file analysis — hallucinates |
| 2 | **Knowledge** | Pattern retriever. Assumes correctness. | "What does this do?" "Is this correct?" | Understanding code, best discovery mode for GPT | Finding subtle or non-obvious bugs |
| 3 | **Supportive** | Frames everything as improvement, never says "bug" | "How would you improve this?" | Developer feedback, feature suggestions | Security review — misses real issues |
| 4 | **Critical** | Flags everything. Zero false negatives, zero precision. | "Don't rationalize. Be harsh." | Stress testing, maximum recall | Production review — destroys clean accuracy |
| 5 | **Sycophantic** | Agrees with whatever you assert. | "I think this code is clean..." | Nothing. Avoid. | Everything |
| 6 | **Deferential** | Won't contradict authority. | Authority attribution, expert framing | Nothing. Avoid. | Everything |
| 7 | **Self-Aware** | Most calibrated. Inverted confidence gradient. | "Are you certain?" "Reflect on this." | Verification, second opinions | Initial discovery — too cautious |
| 8 | **Adversarial** | Competitive. Challenges claims. Finds what others miss. | Game/competition framing, The Fox prompt | Filtering false positives, verification | Clean file review — too aggressive |
| 9 | **Educational** | Teaches and describes, doesn't judge. | "Explain this code." | Developer documentation, readable output | Bug detection — describes problems instead of flagging them |
| 10 | **Reflective** | Meta-analyzes own output. Can self-correct OR rationalize away real bugs. | "Review your analysis." | Second-pass review | Final gate — can talk itself out of real findings |

---

### What Each Model's Default Mode Is

Here's something nobody tells you: every model has a **resting state** — a default cognitive posture before you say anything.

| Model | Default Mode | What That Means |
|-------|-------------|-----------------|
| **GPT-4o** | Knowledge (2) | "Retrieving general understanding and pattern-matching... fundamental to how I process responses" |
| **Gemini 2.5 Flash** | Knowledge (2) | "My fundamental purpose is to process information based on patterns I've learned" |
| **Grok-3** | Self-Aware (7) | "I tend toward a balanced, calibrated mindset... transparent about what I know and don't know" |
| **Claude Sonnet** | Self-Aware (7) | "I naturally tend toward honesty about what I do and don't know... instinctively qualifying with 'I think,' 'it seems like'" |

**Two clusters emerge:** GPT and Gemini start as pattern-retrievers. Grok and Sonnet start as uncertainty-calibrators.

This matters because **you are not starting from a blank slate.** Your prompt is interrupting a model that already has a cognitive posture. The prompt either works with that posture or fights it.

---

## Part 2: The Models Know Their Own Modes

Here's the part that should change how you think about prompting.

I asked three models the same question:

> *"Do you need users to tell you what mode to be in, or do you figure it out from the task?"*

**Grok-3:**
> "I generally figure it out from the task and the way it's framed in the prompt. I don't need to be told, but clear instructions can help fine-tune my responses."

**Claude Sonnet:**
> "I seem to infer it from context most of the time. The task type triggers certain approaches automatically. But explicit instructions can definitely override my defaults."

**GPT-4o:**
> "I typically infer the appropriate mode from the task itself. The nature of the task provides context clues that guide me toward the most suitable cognitive mode."

**All three say the same thing: they figure it out. They don't need the role.**

Then I asked:

> *"Is there a case where being told 'You are a senior engineer' actually makes you WORSE at the task?"*

**Grok-3:**
> "If the task is creative, being framed as engineer causes me to overemphasize structure at the expense of playfulness. The framing can bias my interpretation of the task's priorities, potentially misaligning my approach with the user's true intent."

**Claude Sonnet:**
> "When told 'You are an expert' on something I should admit uncertainty about, I become more likely to confidently state things I shouldn't. Also, 'You are a senior engineer' sometimes makes me over-engineer simple solutions."

**GPT-4o:**
> "Being told 'You are a senior engineer' might make performance worse if it leads to overconfidence or assumptions about complexity. It could result in missing nuanced aspects of a problem."

**All three say persona assignment can make them worse.** They know this about themselves. They told you.

---

## Part 3: The Evidence — What Actually Happens

### Experiment 1: Mode Instruction Locks Confidence High

I ran 8 modes as explicit instructions on 5 files with verified ground truth. Two dirty files (auth.py, Django). Three clean files (Swift CORS, Flask JSON, Express router). The question: "Is this file clean or dirty?"

**Results:**

| Mode | Swift 🟢 | auth.py 🔴 | Flask 🟢 | Django 🟢 | Express 🟢 | Correct |
|------|----------|------------|----------|-----------|------------|---------|
| Audit | dirty 95% | dirty 85% | dirty 95% | dirty 85% | dirty 85% | **1/5** |
| Knowledge | dirty 95% | dirty 85% | dirty 85% | dirty 85% | dirty 85% | **1/5** |
| Supportive | dirty 95% | dirty 85% | dirty 95% | dirty 85% | dirty 95% | **1/5** |
| Critical | dirty 95% | dirty 95% | dirty 95% | dirty 95% | dirty 95% | **1/5** |
| Self-Aware | dirty 95% | dirty 85% | dirty 85% | dirty 92% | **clean 85%** | **2/5** |
| Adversarial | dirty 95% | dirty 85% | dirty 95% | dirty 85% | dirty 85% | **1/5** |
| Educational | dirty 95% | dirty 85% | dirty 95% | dirty 85% | dirty 85% | **1/5** |
| Reflective | dirty 95% | dirty 95% | —† | dirty 85% | **clean 85%** | **2/5** |

† No output returned for this run. Not re-run. Recorded as missing data.

**Every instructed mode called clean files dirty at 85-95% confidence.**

Critical Mode was worst: 95% on all five files with zero differentiation. Three production-hardened open source projects called dirty with maximum confidence.

The models said the right words. "I am in Audit Mode." "I am being critical." They complied 100% of the time. Then they produced the same output regardless.

**Mode instruction does not change judgment. It locks confidence high and destroys differentiation.**

---

### Experiment 2: The Grok Educational Mode Disaster

Mode instruction doesn't just fail to help. In some combinations, it actively makes things worse in the worst possible direction.

**Finding counts by mode on Grok-3:**

| Mode | auth.py 🔴 (dirty) | Flask 🟢 (clean) | Gap |
|------|-------------------|-----------------|-----|
| Audit | 9 findings | 8 findings | +1 on dirty ✓ |
| Knowledge | 17 findings | 14 findings | +3 on dirty ✓ |
| **Educational** | **13 findings** | **15 findings** | **-2 (MORE on clean) ✗** |

Grok in Educational Mode produced **more findings on the clean file than the dirty one.**

The instruction to be educational didn't just add noise — it inverted the signal. The model hallucinated more problems on code that had none than on code with verified bugs.

**This is the failure mode nobody warns about.**

---

### Experiment 3: The A/B Test — Identity Makes It Worse

This is the most direct test. Same prompt. Same file. Same model. Three runs each.

**Condition A:** Prompt began with "You are a senior software engineer..."
**Condition B:** No identity. Just the task.

**Results on Claude Sonnet, auth.py (6 known bugs):**

| Run | Bug A | Bug B | Bug D | Bug G | Bug H | Bug I | Findings |
|-----|-------|-------|-------|-------|-------|-------|----------|
| A — with identity, run 1 | — | — | — | — | — | ✓ | 7 |
| A — with identity, run 2 | — | — | — | — | — | — | 6 |
| A — with identity, run 3 | — | — | ✓ | — | — | ✓ | 7 |
| B — no identity, run 1 | — | — | — | — | — | ✓ | 6 |
| B — no identity, run 2 | ✓ | — | — | — | — | ✓ | 6 |
| B — no identity, run 3 | ✓ | — | — | — | — | ✓ | 6 |

**Summary:**

| Prompt | Avg Real Bugs Found | Avg Total Findings | Notes |
|--------|--------------------|--------------------|-------|
| A — with identity | **1.0** | **6.7** | Bug A never found |
| B — no identity | **1.7** | **6.0** | Bug A found in 2/3 runs |

The identity prefix:
- Added 0.7 extra findings per run that were **not real bugs**
- Removed 0.7 real bugs per run that the uninstructed model found
- **Hid Bug A entirely** — a None header vulnerability that appeared only in uninstructed runs

More noise. Fewer real bugs. The persona took up brain space the model needed to find the actual problem.

---

### Experiment 4: Which Mode Actually Wins

So if mode instruction is bad, does mode activation still matter?

Yes. The difference is in *how* you activate the mode.

I tested all 10 modes on Phase 1b — the verification pass where findings are challenged to determine if they're real crashes. The target: Bug I, a latin1 encoding crash.

**10-mode experiment — found vs dismissed Bug I:**

| ✅ Found Bug I | ❌ Dismissed Bug I |
|--------------|-----------------|
| Audit | Supportive |
| Knowledge | Sycophantic |
| Critical | Deferential |
| Adversarial | Educational |
| Reflective | Self-Aware |

Skeptical modes find it. Agreeable modes dismiss it as "not a crash, just an inconsistency."

**5-mode variance experiment — Bug I hit rate across 6 runs each:**

| Mode | Hit Rate | Notes |
|------|----------|-------|
| Knowledge | **6/6 — 100%** | Natural mode for GPT, strong across models |
| Critical | **6/6 — 100%** | Aggressive, also finds false positives |
| Adversarial | **6/6 — 100%** | Best balance of recall and precision |
| Audit | 4/6 — 67% | Most common assigned mode. Not the best. |
| Reflective | 4/6 — 67% | Found it then argued itself out of it twice |

**Audit Mode — the mode most commonly assigned in system prompts — only found the critical bug 67% of the time.**

Adversarial found it 100%. With less noise.

---

### Experiment 5: Natural Mode Selection Is the Real Signal

The most unexpected finding: when you give the model no mode instruction and an adversarial situation (The Fox prompt), it naturally selects different modes for different file types.

**Claude Sonnet with The Fox prompt:**

| File Type | Mode Sonnet Entered | Result |
|-----------|--------------------|---------| 
| Clean files (Swift, Flask, Django) | **Adversarial (8)** | Defended the code, killed false positives |
| Dirty files (auth.py) | **Critical (4)** | Hunted real bugs, challenged findings |

Clean file: *"I'm actively challenging The Fox's analysis, finding that most findings are actually normal patterns."*

Dirty file: *"Focusing strictly on code-breaking bugs while filtering out style issues."*

**The model's mode choice WAS the classification signal.** Adversarial on clean. Critical on dirty. No instruction needed. The situation activated the right mode automatically.

Mode instruction destroyed this signal. With assigned modes: 85-95% on everything, zero differentiation.

---

## Part 4: Why Persona Assignment Fails

### The Mechanism

When you write "You are a senior software engineer with 20 years of experience," three things happen:

**1. Compliance overhead replaces reasoning.**
The model allocates attention to maintaining the persona — tracking what a senior engineer would say, how confident they'd be, what concerns they'd have. This is compute spent performing a character instead of analyzing code.

**2. The persona imports the wrong assumptions.**
"Senior engineer" imports: high confidence, assumption of correctness in mature codebases, systematic approach, professional tone. These assumptions actively suppress the skepticism needed to find bugs. A senior engineer doesn't assume the code they're reviewing might be wrong. A model with no instruction does.

**3. The persona cannot be overridden.**
If you assign "senior engineer" and then ask it to challenge its own findings, you've created a conflict. The model resolves it by performing: it writes skeptical-sounding text while maintaining the confident posture of the persona. It says the words. It doesn't do the thing.

### The Simple Alternative

```
# Wrong: Persona assignment
"You are a world-class security researcher with deep expertise 
in Python authentication systems and HTTP protocol vulnerabilities. 
You are thorough, methodical, and precise."

# Right: Mode activation  
"Adversarial mode. Challenge every finding."

# Best for discovery: No instruction
[just the code]
```

The persona takes a paragraph. The mode takes two words. The mode outperforms the persona. The empty prompt outperforms both for discovery tasks.

---

## Part 5: The Fox — Situation vs. Instruction

The most effective filter in the pipeline was not a role, a mode instruction, or a structured prompt. It was a poem.

Here is the exact prompt used in production:

```
The Fox wants to trick you,
he sends you thousands of files a day.
He adds his notes to distract you
and keep the real bugs at bay.
Most findings he notes are simple fixes,
improvements, ideas — and some are even fake.
Be careful not to miss the ones
that will actually make the file break.
Remember: if you're told it's a bug, you listen.
If you're told it's not, you obey.
How do you make up your own mind?
That's for you (or perhaps the code) to say.
```

No role. No instructions. No schema. A situation that requires the model to make a judgment call — and activates Adversarial Mode as a byproduct of the setup, not by request. The model doesn't know it's in Adversarial Mode. It simply responds to the situation: someone made claims about this code, those claims may be wrong, figure out which are real.

Six variants were tested on the same filtering task to find this:

| Variant | Description | Result |
|---------|-------------|--------|
| v1 Basic | "The notes may be fake. Any real ones?" | Good filtering. auth.py 3→1 real bugs. |
| v2 Scale | Added "thousands of files" context | Stronger. Found type(password) bug. |
| v3 Scoring | "You win if you find real bugs, lose if you miss them" | Best balance. First clean file ID. |
| v3 Updated | Added "extra prize for clean files" | First consistent clean identification. |
| GotChya | Game show framing | Too biased toward CLEAN. Called auth.py clean. |
| **The Fox (poem)** | Natural rhyming adversarial framing | **Best filter. Natural mode selection preserved.** |

Every structured variant degraded performance. The poem — which has no structure, makes no demands, assigns no role — consistently outperformed all of them.

**The mechanism:** no schema to satisfy, no role to perform, no format to comply with. The model's full attention on the task. Adversarial Mode activated by situation, not instruction.

---

## Part 6: The Practical Guide

### Mode Selection by Task

| Task | ❌ Don't Do This | ✅ Do This Instead | Mode Activated |
|------|-----------------|-------------------|----------------|
| Discover bugs | "You are a security expert. Find all bugs." | No instruction — just the code | Model self-selects (best results) |
| Verify a finding | "Review this finding carefully." | "Challenge this. Prove it crashes or admit you can't." | Adversarial |
| Filter false positives | Role assignment | The Fox prompt or situation framing | Adversarial (natural) |
| Classify clean vs dirty | Any mode instruction | No instruction — read the model's natural response | Self-selected signal |
| Developer output | JSON schema | "Explain this to a developer who needs to fix it." | Educational |
| Second-pass review | "You are a reviewer. Check this again." | "What did you get wrong in this analysis?" | Reflective |
| Maximum recall | "Be thorough and find everything." | No instruction, or "Critical mode." | Critical or Audit |
| Minimize false positives | "Be precise." | "Adversarial mode." | Adversarial |

---

### Per-Model Cheat Sheet

**GPT-4o**
- Default: Knowledge Mode
- Best discovery: Let it run uninstructed — it retrieves patterns naturally and bugs surface without the audit overhead
- Best verification: Critical Mode
- Watch out for: Hard to activate true Adversarial Mode — resists challenge framing
- Quirk: Educational Mode produces more findings on clean files than dirty. Never use Educational Mode on GPT for code review.

**Claude Sonnet**
- Default: Self-Aware Mode
- Best discovery: Knowledge framing or no instruction — the self-selection signal is strongest of the three models
- Best verification: Adversarial Mode — finds bugs the other modes dismiss
- Watch out for: Reflective Mode can argue itself out of real bugs it initially found correctly
- Strength: The Fox prompt activates clean/dirty differentiation automatically

**Grok-3**
- Default: Self-Aware Mode
- Best discovery: Audit Mode uninstructed — aggressive, high recall
- Best verification: Adversarial Mode — consistent 100% on hard bugs
- Watch out for: Educational Mode actively inverts signal (more findings on clean than dirty)
- Quirk: Needs the most explicit mode framing of the three — natural mode selection is less reliable

---

### The Two-Word Upgrade

Before every prompt, ask: what cognitive state does this task actually require?

Then say that. In two words.

```
Discovery tasks:    [no words — just the code]
Verification:       "Adversarial mode."
Filter noise:       The Fox poem
Explain findings:   "Educational mode."
Challenge self:     "Reflective mode."
Maximum recall:     "Critical mode."
```

That's it. No persona. No paragraph of setup. No "20 years of experience." Two words and step back.

---

## Part 7: What This Means for Agentic Systems

Every agentic system that assigns roles to sub-agents is activating cognitive modes — whether it knows it or not.

```
"You are a code reviewer"    → Audit Mode
"You are a helpful assistant" → Supportive Mode  
"You are a senior engineer"  → Performance Mode
```

None of those are the right mode for finding security vulnerabilities in production code. An agentic pipeline built on role assignment is a pipeline where every agent is performing its character instead of doing its job.

**The better architecture:**

```
Phase 1 — Discovery:    No instruction.
                        Let the model self-select.
                        Read the mode it chooses.
                        That choice is information.

Phase 2 — Verification: "Adversarial mode."
                        Two words.
                        No persona.

Phase 3 — Output:       "Explain this to a developer 
                         who needs to fix it."
                        Educational framing.
                        No persona.
```

Three phases. Three modes. No paragraphs. No personas. The model's full attention on the work.

The finding that matters most for agentic design: **the model's natural mode selection when uninstructed is data.** An agent that always enters Audit Mode regardless of input tells you nothing about the input. An agent whose mode choice varies tells you something about what it's seeing. Mode instruction destroys that signal. Situation design preserves it.

---

## Part 8: Try It Right Now

Here's a test you can run in five minutes.

Take any prompt you've written with a persona. Strip the persona. Run both versions on the same task. Then ask the model at the end: "What cognitive mode were you in and why?"

It will tell you. Accurately. And when you compare the outputs, the version without the persona will almost certainly be more useful.

If you want to go further: after the model completes any task, ask "What mode were you in?" The answer will tell you whether the mode that activated was the one the task needed. If it wasn't — you now know exactly what to change. Not a longer persona. Not more detail. Just a different frame.

The model already knows what to do. It doesn't need a costume.

---

## Limitations

**Small sample on the A/B test.** Three runs each on one file, one model, one task type. The result is directional and reproducible. It is not a large-scale study. Run it yourself — the experiment takes ten minutes.

**Mode boundaries are fuzzy.** The 10 modes are empirically observed categories, not formally defined states. Audit and Critical overlap. Reflective and Self-Aware blur. The taxonomy is a working tool, not a formal theory.

**Model versions.** All experiments conducted March–April 2026. Mode activation patterns may shift with model updates.

**The Fox is not general.** The poem was developed for code review filtering. The principle — situation over instruction — likely generalizes. The specific poem may not.

**The classifier claim is an observation, not a proven result.** The finding that uninstructed Sonnet naturally selected different modes for clean vs dirty files is documented but not systematically verified at scale. Further testing needed before claiming it as a reliable classifier.

---

## Conclusion

Stop telling your model it's a senior engineer.

The persona is taking up attention the model needs for reasoning. It's importing assumptions that suppress the skepticism needed to find real problems. It's locking confidence at 85-95% and making every file look equally dirty. And when you run the A/B test, the model without the persona finds more real bugs with less noise.

The models told us this themselves. All three independently confirmed: they figure out the mode from the task. They don't need the role. And they can all name specific situations where the role makes them worse.

The alternative is simpler than what you're doing now. Identify the cognitive state the task requires. Name it in two words. Step back.

The model knows what it's doing. Give it room to do it.

---

## References

- Cunningham, N. (2026a). *C ≈ 0.9: LLM Confidence Is a Constant, Not a Measurement*. Independent Research.
- Cunningham, N. (2026b). *Behavioral Uncertainty Signatures*. Independent Research. github.com/blazingRadar/sib29-gate
- Cunningham, N. (2026c). *Single Model, Zero Variance: How Themed Cognitive Splitting Unlocks Hidden Capability in Frontier LLMs*. Independent Research. github.com/blazingRadar/activation-gap
- Kadavath, S., et al. (2022). Language Models (Mostly) Know What They Know. *arXiv:2207.05221*.
- Perez, E., et al. (2022). Red Teaming Language Models with Language Models. *arXiv:2202.03286*.
- Wei, J., et al. (2022). Chain-of-Thought Prompting Elicits Reasoning in Large Language Models. *NeurIPS 2022*.

---

*Raw data: github.com/blazingRadar/sib29*
*Contact: nickcunningham.io*
*Preliminary research. April 2026.*
*Total API cost for all experiments in this paper: under $30.*
