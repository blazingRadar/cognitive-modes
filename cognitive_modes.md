# Cognitive Modes

**Nick Cunningham**  
Independent research notes, April 2026

## Summary

This note summarizes preliminary observations from prompt-framing experiments in LLM code review. The working hypothesis is that prompt framing can push a model toward different response behaviors, such as explanation, bug discovery, critique, or deference.

The public repository does not include the raw transcripts, tested source files, scoring scripts, or full result tables. The claims below are therefore bounded as case-study observations and hypotheses.

## Observed Pattern

In the observed runs, similar code-review tasks produced different outputs depending on framing.

For example:

- Explanation framing tended to produce summaries and assumptions of correctness.
- Bug-discovery framing tended to produce more defect candidates.
- Adversarial framing tended to challenge prior findings more aggressively.
- Educational framing sometimes produced explanatory output rather than useful audit findings.
- Persona framing sometimes appeared to increase confidence without improving useful findings.

These labels are descriptive shorthand for observed output styles. They are not validated categories of model cognition.

## Why This Matters

Code review and bug verification are not the same task. A prompt that works for discovery may be too noisy for verification. A prompt that works for explanation may be too deferential for audit.

The practical lesson is to choose framing based on the desired review behavior:

| Task | Safer Framing Hypothesis |
|---|---|
| First-pass discovery | Direct bug-discovery request with minimal role instruction. |
| Verification | Skeptical or adversarial review against concrete evidence. |
| Explanation | Neutral explanation request after findings are already known. |
| Cross-review | De-identified findings with source code available for checking. |

This is a workflow hypothesis, not a proven rule.

## Persona Assignment

One recurring observation was that assigning a professional persona could change review behavior. In some runs, persona-framed prompts produced more confident prose and less useful bug discovery than neutral prompts.

The conservative recommendation is to avoid unnecessary persona framing during code-audit discovery. Ask for the task directly, then verify findings separately.

## Mode Labels

The original notes used labels such as audit mode, knowledge mode, adversarial mode, educational mode, sycophantic mode, and deferential mode. These labels are useful for discussing failure modes, but they should not be read as a validated taxonomy.

The evidence in this repository supports only a weaker claim: output behavior appeared to cluster by framing in the observed runs.

## Confidence And Calibration

The experiments also suggested that self-reported confidence was not a reliable enough signal for code-review correctness. The public notes do not include the measurement artifacts needed to quantify that claim, so it remains a hypothesis in this archive.

The safer operational rule is to verify claims against code and tests rather than relying on model-stated confidence.

## Practical Guidance

- Prefer direct task framing over persona assignment for first-pass bug discovery.
- Separate discovery from verification.
- Use skeptical framing for verification, not necessarily for first-pass recall.
- Remove irrelevant attribution when asking a model to review another model's output.
- Treat confidence language as weak evidence unless it is backed by concrete code references.

## What This Does Not Prove

- It does not prove that one prompt always outperforms another.
- It does not prove that a fixed set of cognitive modes exists.
- It does not prove a production calibration method.
- It does not prove statistical significance.
- It does not include raw evidence sufficient for independent reproduction.

## Why Keep This Repository

The repository preserves a useful early observation for agentic code-review workflows: prompt framing can change what a model pays attention to, and mismatched framing can produce noisy or misleading review output.

That observation is narrow, but it remains relevant to audit-pipeline design, prompt selection, and multi-model review workflows.
