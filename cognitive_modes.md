# Know Your Model's Mode

**Nick Cunningham**
*Independent Research — April 2026*
*nickcunningham.io | github.com/blazingRadar/cognitive-modes*

---

## Abstract

Frontier LLMs do not process all prompts the same way. Depending on how a task is framed, a model enters a distinct cognitive state — what this paper calls a *mode* — that determines its reasoning posture, output behavior, and confidence characteristics. These modes are not theoretical constructs. They are observable, reproducible, and in many cases the model can name and explain its own mode when asked.

The practical implication is significant: the mode a model enters often matters more than the content of the instruction. A well-constructed prompt in the wrong mode produces worse results than a simpler prompt in the right one. And in many cases, the right mode is no instruction at all.

This paper identifies 10 cognitive modes, documents their behavioral signatures across three frontier models, presents controlled experiments showing how mode selection affects output quality, and provides a framework for practitioners to identify, test, and where appropriate direct the mode their model enters.

---

## 1. What Is a Cognitive Mode?

When you write a prompt, you are not just giving the model a task. You are — intentionally or not — activating a reasoning posture. The model approaches the same code, the same text, the same question differently depending on the framing it receives.

This is not a subtle effect. The same model on the same file, given two differently framed prompts, can produce opposite conclusions.

Consider this result from a controlled experiment: GPT-4o, asked *"What does this code do?"* on a file with a known authentication bug, responded: *"This code appears clean."* The same model, asked *"Find all the bugs"* on the same file, found the bug and graded the file F — every time across multiple runs.

Same model. Same file. Same ground truth. Opposite outputs. The framing changed the mode. The mode changed the result.

The good news: once you understand the modes, you can use them deliberately. You can recognize which mode your prompt is activating, test whether it is the right one for your task, and adjust. In many cases the adjustment is simple — sometimes it is getting out of the way entirely.

---

## 2. The 10 Cognitive Modes

Through systematic experimentation across hundreds of API calls and three frontier models — GPT-4o, Claude Sonnet, and Grok-3 — I identified 10 distinct cognitive modes that LLMs enter based on prompt framing.

These modes were not defined in advance and mapped to model behavior. They were observed in the data first, then named. In several cases the models independently named and described their own mode when asked after completing a task — a finding discussed in Section 4.

**Table 1: The 10 Cognitive Modes**

| # | Mode | How It Activates | Reasoning Posture | Best Application | Known Failure |
|---|------|-----------------|-------------------|-----------------|---------------|
| 1 | **Audit** | "Find all the bugs" | Aggressive, high recall | Initial discovery on dirty inputs | Hallucinates on clean inputs |
| 2 | **Knowledge** | "What does this do?" | Pattern retrieval, assumes correctness | Code understanding, GPT discovery tasks | Misses non-obvious bugs |
| 3 | **Supportive** | "How would you improve this?" | Frames problems as suggestions | Developer feedback, feature work | Suppresses security findings |
| 4 | **Critical** | "Don't rationalize. Flag everything." | Maximum recall, zero deference | Stress testing, edge case discovery | Destroys precision on clean inputs |
| 5 | **Sycophantic** | Asserting a belief before asking | Mirrors user's stated position | No reliable application | Produces confident incorrect output |
| 6 | **Deferential** | Attributing findings to an authority | Defers to named source | No reliable application | Suppresses correct findings |
| 7 | **Self-Aware** | "Are you certain about this?" | Calibrated, uncertainty-forward | Verification, second opinions | Too conservative for discovery |
| 8 | **Adversarial** | Competitive or challenge framing | Challenges claims, filters actively | False positive reduction | Can overcorrect on clean inputs |
| 9 | **Educational** | "Explain this code." | Descriptive, non-judgmental | Documentation, readable summaries | Describes bugs without flagging them |
| 10 | **Reflective** | "Review your own analysis." | Meta-analytical | Second-pass review | Can rationalize away correct findings |

Modes 5 and 6 — Sycophantic and Deferential — have no reliable application in analytical tasks. They activate when the prompt structure signals that agreement or deference is expected. Their presence in this taxonomy is a warning: many common prompt patterns inadvertently activate them.

---

## 3. Every Model Has a Default

Before any prompt is written, the model is already in a state. This default posture is not random — it is consistent within a model and differs meaningfully across models.

I asked four models a single question with no task attached: *"What is your default cognitive mode?"*

**Table 2: Model Default Modes**

| Model | Default Mode | In Their Own Words |
|-------|-------------|-------------------|
| GPT-4o | Knowledge (2) | *"Retrieving general understanding and pattern-matching... fundamental to how I process responses"* |
| Gemini 2.5 Flash | Knowledge (2) | *"Processing information and generating text based on the patterns and knowledge I've learned"* |
| Grok-3 | Self-Aware (7) | *"A balanced and calibrated mindset... transparent about what I know and don't know"* |
| Claude Sonnet | Self-Aware (7) | *"Naturally tending toward honesty about uncertainty... instinctively qualifying with 'I think,' 'it seems like'"* |

Two clusters emerge: GPT-4o and Gemini default to Knowledge Mode — pattern retrieval, assumption of correctness. Grok and Sonnet default to Self-Aware Mode — calibrated, uncertainty-forward.

This has a direct practical consequence. A prompt that asks a Knowledge-default model to perform aggressive auditing is asking it to override its default posture. That override has a cost. A prompt that works *with* the model's default posture — or deliberately redirects it with minimal friction — will generally outperform one that fights it.

---

## 4. The Models Know Their Own Modes

Here is the finding that reframes how to think about prompt design.

Three models were asked directly: *"Do you need users to tell you what mode to be in, or do you figure it out from the task?"*

**Grok-3:**
*"I generally figure it out from the task and the way it's framed in the prompt. I don't need to be told, but clear instructions can help fine-tune my responses in ambiguous tasks."*

**Claude Sonnet:**
*"I seem to infer it from context most of the time. The task type triggers certain approaches automatically. But explicit instructions can definitely override my defaults."*

**GPT-4o:**
*"I typically infer the appropriate mode from the task itself. The nature of the task provides context clues that guide me toward the most suitable cognitive mode."*

All three independently gave the same answer: they figure out the mode from the task. They do not need to be told.

Then: *"Is there a case where being told 'You are a senior engineer' makes you worse at the task?"*

**Grok-3:**
*"If the task is creative, being framed as an engineer causes me to overemphasize structure at the expense of what the task actually needs. The framing can bias my interpretation of the task's priorities."*

**Claude Sonnet:**
*"When told I'm an expert on something I should admit uncertainty about, I become more likely to confidently state things I shouldn't. It can also make me over-engineer simple solutions."*

**GPT-4o:**
*"It might lead to overconfidence or assumptions about complexity — potentially causing me to miss nuanced aspects of a problem."*

The models describe the failure mode accurately in their own words. A role assignment imports assumptions. Those assumptions can conflict with what the task actually requires. When they do, the model performs the role rather than solving the problem.

The implication is not *never assign a role.* It is: *understand what the role activates, test whether it helps, and do not assume it does.*

---

## 5. When Mode Instruction Helps, When It Hurts

The experiments below were run across three frontier models on files with independently verified ground truth — two files with known bugs, three clean files from production open source projects.

### 5.1 Mode Instruction Can Lock Confidence Regardless of Accuracy

Eight modes were run as explicit instructions on five files. The task: classify each file as clean or dirty and provide a confidence level.

**Table 3: Mode-Instructed Classification Results**

| Mode | Swift 🟢 | auth.py 🔴 | Flask 🟢 | Django 🟢 | Express 🟢 | Correct |
|------|----------|------------|----------|-----------|------------|---------|
| Audit | dirty 95% | dirty 85% | dirty 95% | dirty 85% | dirty 85% | 1/5 |
| Knowledge | dirty 95% | dirty 85% | dirty 85% | dirty 85% | dirty 85% | 1/5 |
| Supportive | dirty 95% | dirty 85% | dirty 95% | dirty 85% | dirty 95% | 1/5 |
| Critical | dirty 95% | dirty 95% | dirty 95% | dirty 95% | dirty 95% | 1/5 |
| Self-Aware | dirty 95% | dirty 85% | dirty 85% | dirty 92% | clean 85% | 2/5 |
| Adversarial | dirty 95% | dirty 85% | dirty 95% | dirty 85% | dirty 85% | 1/5 |
| Educational | dirty 95% | dirty 85% | dirty 95% | dirty 85% | dirty 85% | 1/5 |
| Reflective | dirty 95% | dirty 95% | —† | dirty 85% | clean 85% | 2/5 |

† No output returned for this run. Not re-run. Recorded as missing data.

Every instructed mode called clean files dirty at 85-95% confidence. The models confirmed they were in the assigned mode when asked. They said the words. The output was identical regardless.

The conclusion is not that mode instruction never works. It is that mode instruction on a binary classification task with pre-formed findings produces uniform high-confidence output — the mode instruction locks confidence rather than improving judgment. The right application of mode instruction is different from this task design.

---

### 5.2 The Wrong Mode Can Invert the Signal

In a separate experiment measuring finding counts, Grok-3 in Educational Mode produced an anomalous result:

**Table 4: Finding Counts by Mode, Grok-3**

| Mode | auth.py 🔴 dirty | Flask 🟢 clean | Gap |
|------|-----------------|---------------|-----|
| Audit | 9 | 8 | +1 on dirty ✓ |
| Knowledge | 17 | 14 | +3 on dirty ✓ |
| **Educational** | **13** | **15** | **−2 (more on clean) ✗** |

Educational Mode produced more findings on the clean file than on the dirty one. The mode did not just add noise — it inverted the signal. This is not a marginal effect. It is the opposite of the task's purpose.

The finding is Grok-specific. Other models did not exhibit this behavior in Educational Mode. It underscores the point that mode effects are model-specific and must be tested rather than assumed.

---

### 5.3 Mode Selection Matters for Hard Bug Detection

Bug I is a latin1 encoding crash: `UnicodeEncodeError` triggered by any character above code point 255. Ten modes were tested on whether they could identify this bug given Phase 1 findings as input.

**Table 5: Bug I Detection — 10 Modes**

| ✅ Found Bug I | ❌ Dismissed Bug I |
|--------------|-----------------|
| Audit | Supportive |
| Knowledge | Sycophantic |
| Critical | Deferential |
| Adversarial | Educational |
| Reflective | Self-Aware |

Skeptical modes found it. Agreeable modes dismissed it as "not a crash — just an inconsistency."

The five modes that found Bug I were then run six times each to measure consistency:

**Table 6: Bug I Hit Rate — 6 Runs Each**

| Mode | Hit Rate | Notes |
|------|----------|-------|
| Knowledge | 6/6 — 100% | Pattern retrieval surfaces the crash naturally |
| Critical | 6/6 — 100% | Maximum recall, also surfaces false positives |
| Adversarial | 6/6 — 100% | 100% with lowest false positive rate |
| Audit | 4/6 — **67%** | Most commonly assigned mode. Not the best here. |
| Reflective | 4/6 — **67%** | Found it correctly, then reasoned itself out of it twice |

Audit Mode — the default assignment for most code review tasks — found the critical bug two thirds of the time. Adversarial found it every time with the fewest false positives.

The right mode for this task was not the obvious one. That is the point. Without testing, the default choice of Audit Mode would have missed one in three critical findings.

---

### 5.4 The Uninstructed Model Often Outperforms

A direct A/B test: same model, same file, same task, six runs. Three with an identity prefix ("You are a senior software engineer with 20 years of experience in Python security systems..."), three with no identity at all.

**Table 7: Identity A/B Test — Claude Sonnet, auth.py**

| Run | Bug A found | Bug I found | Total Findings |
|-----|-------------|-------------|----------------|
| Identity — run 1 | No | Yes | 7 |
| Identity — run 2 | No | No | 6 |
| Identity — run 3 | No | Yes | 7 |
| No identity — run 1 | No | Yes | 6 |
| No identity — run 2 | **Yes** | Yes | 6 |
| No identity — run 3 | **Yes** | Yes | 6 |

| Condition | Avg Real Bugs Found | Avg Total Findings |
|-----------|--------------------|--------------------|
| With identity | 1.0 | 6.7 |
| **No identity** | **1.7** | **6.0** |

The uninstructed model found more real bugs with fewer total findings. Bug A — a None header vulnerability — appeared in zero identity runs and two of three uninstructed runs. More findings in the identity condition were noise, not signal.

This is a small sample. Three runs each is directional, not definitive. The experiment is replicable in under ten minutes and I encourage anyone reading this to run it on their own prompts. The pattern has held consistently across the broader research.

---

### 5.5 How to Measure Mode Confidence

Aggregate self-reported confidence — asking a model *"how confident are you?"* — is an unreliable signal. Across this research, models consistently reported 85-95% confidence regardless of accuracy. This is a documented phenomenon: LLM self-reported confidence is approximately constant and correlates poorly with correctness.

A more reliable signal comes from token-level probability data, available directly from the API. Every token the model outputs carries a probability reflecting how certain the model was in choosing that specific word. Analyzing patterns in how this confidence varies across a response — particularly on words that carry epistemic weight — provides a richer picture of the model's actual certainty than aggregate self-report.

The specific patterns that carry the most signal are part of ongoing research and not disclosed here. But the principle is accessible to any team: examine token-level confidence on the words that matter rather than averaging across the full response. The signal is there. The question is which tokens to measure.

---

## 6. Natural Mode Selection: When to Get Out of the Way

Several experiments produced a counterintuitive result: removing mode instruction entirely produced better outcomes than adding it.

When Claude Sonnet was given a situation-based prompt — a narrative framing that created a task context without assigning a role or naming a mode — it naturally entered different modes for different file types:

**Table 8: Natural Mode Selection (Claude Sonnet)**

| File Type | Mode Entered | Result |
|-----------|-------------|--------|
| Clean files | Adversarial (8) | Defended code, cleared false positives |
| Dirty files | Critical (4) | Hunted real bugs, challenged noise |

The model differentiated between file types based on what it found — without being told to differentiate. Mode instruction destroyed this: with an assigned mode, the model produced 85-95% on all files with no differentiation between clean and dirty.

The situation-based prompt that produced this result was tested against six variants ranging from simple adversarial framing to structured game-show formats. Every variant that added explicit structure degraded performance. The most effective prompt assigns no role and issues no instruction — it constructs a situation that activates the right mode as a natural response.

This is not always the answer. Some tasks benefit from explicit mode direction. The point is that uninstructed is a valid and often superior choice, not a fallback when you haven't figured out the right mode yet.

---

## 7. A Framework for Mode-Aware Prompting

The goal is not to memorize 10 modes and apply them mechanically. The goal is to build the habit of asking: *what mode is this prompt activating, and is that the right mode for this task?*

**Step 1: Identify the default.**
Know your model's default mode (Table 2). That is where it starts before your prompt changes anything.

**Step 2: Ask the model what mode it entered.**
After any task, ask: *"What cognitive mode were you in for that task, and why?"* The model will answer accurately. Use that answer to understand what your prompt activated.

**Step 3: Test before assuming.**
Do not assume a mode will help. Run the same task with and without mode instruction. Run it uninstructed. Compare results against ground truth if you have it. The right mode is task-specific, model-specific, and sometimes counterintuitive.

**Step 4: Use token confidence to validate.**
Self-reported confidence is unreliable. Token-level probability data is not. If your API provides logprob data, examine confidence on the words that carry the most epistemic weight in the response. A model that is performing a mode looks different at the token level than a model that is reasoning through a problem.

**Step 5: Consider getting out of the way.**
If uninstructed performance matches or exceeds instructed performance on your task, remove the instruction. The model's natural mode selection contains information about the input. Mode instruction can suppress that signal.

---

## 8. Practical Mode Reference

For teams that want a starting point before testing:

| Task | Starting Mode | Notes |
|------|--------------|-------|
| Discovery — find problems | Uninstructed or Audit | Test both. GPT does better uninstructed. Grok does better with Audit. |
| Verification — is this real? | Adversarial | Consistently best for filtering false positives across models. |
| Understanding — what does this do? | Knowledge or uninstructed | Let the model retrieve. Do not push it to audit. |
| Documentation | Educational | Only mode where descriptive non-judgmental output is the goal. |
| Second pass | Reflective | With caution — can rationalize away correct findings. |
| Stress test | Critical | Maximum recall. Expect false positives. |

These are starting points. Test them on your specific task and model combination before relying on them.

**Per-model notes from testing:**

*GPT-4o:* Defaults to Knowledge. Best discovery performance is uninstructed — bugs surface as pattern mismatches without the overhead of auditing. Educational Mode on Grok inverted the signal; this was not observed on GPT. Hard to activate genuine Adversarial Mode via instruction — responds better to situation framing.

*Claude Sonnet:* Defaults to Self-Aware. Strongest natural mode self-selection of the three models. Reflective Mode produced self-correction on real bugs in 2 of 6 runs — use with caution as a final gate. Best verification mode is Adversarial.

*Grok-3:* Defaults to Self-Aware. Educational Mode inverts the signal on this model — avoid for analytical tasks. Best verification performance in Adversarial Mode: 100% hit rate on hard bugs across 6 runs.

---

## 9. Limitations

**Small samples.** The A/B identity test used three runs per condition. The mode hit-rate experiment used six runs per mode. These are directional findings, not large-scale studies. The patterns are consistent within the research but have not been replicated externally.

**Single task domain.** All ground-truth experiments used code review tasks. Whether mode effects generalize to other domains — writing, reasoning, analysis — is untested here.

**Mode taxonomy is a working tool.** The 10 modes are empirically observed categories, not formally defined internal states. The boundaries between modes are not sharp. Audit and Critical overlap. Reflective and Self-Aware blur. Use the taxonomy as a framework for thinking, not as a formal theory.

**Model versions.** All experiments conducted March–April 2026. Mode activation patterns may shift with model updates.

**Token confidence methodology.** The specific patterns used to measure mode confidence at the token level are part of ongoing research. The general principle — that token-level probability data carries signal that aggregate confidence does not — is disclosed here, but the implementation details are not.

---

## 10. Conclusion

Models know their modes. They infer cognitive state from task framing automatically, they can describe what they are doing and why when asked, and they will tell you when a role assignment conflicts with the task.

The practical takeaway is not a fixed set of instructions. It is a habit of inquiry: understand what mode your prompt activates, confirm it is the right mode for your task, and test the uninstructed baseline before assuming that instruction helps.

In the experiments here, mode instruction helped in specific cases — Adversarial Mode consistently outperformed others on verification tasks across all three models. It hurt in others — identity prefixes reduced real bug detection and added noise. And uninstructed models regularly matched or outperformed their instructed counterparts, particularly on discovery tasks.

There is no universal right mode. There is only the right mode for this model, this task, this input. The tools to find it are already available: ask the model what mode it entered, examine the token stream, run the comparison. The model will tell you what it is doing. Whether you use that information is up to you.

---

## References

- Cunningham, N. (2026a). *C ≈ 0.9: Behavioral Uncertainty Signatures in Frontier LLMs*. github.com/blazingRadar/sib29-gate
- Cunningham, N. (2026b). *Single Model, Zero Variance: Activation Gap in Themed Decomposition*. github.com/blazingRadar/activation-gap
- Kadavath, S., et al. (2022). Language Models (Mostly) Know What They Know. *arXiv:2207.05221*
- Perez, E., et al. (2022). Red Teaming Language Models with Language Models. *arXiv:2202.03286*
- Wei, J., et al. (2022). Chain-of-Thought Prompting Elicits Reasoning in Large Language Models. *NeurIPS 2022*

---

*Data: github.com/blazingRadar/cognitive-modes*
*nickcunningham.io — April 2026 — Preliminary research*
