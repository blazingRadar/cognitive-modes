# Cognitive Modes

This repository is a small research-notes archive on how prompt framing appeared to change LLM code-review behavior in a set of preliminary experiments.

The core claim is bounded: in the observed runs, changing prompt framing changed the kinds of answers models produced. Some frames encouraged bug discovery, some encouraged explanation, and some produced noisier or less useful review behavior.

This is not a benchmark suite, a production evaluator, or a general proof of model-family behavior. The public artifact contains a synthesis note and a claim boundary, not the raw transcript bundle.

## What Is Here

- [cognitive_modes.md](cognitive_modes.md): bounded synthesis note on prompt framing, mode activation, and review behavior.
- [PUBLICATION_MANIFEST.md](PUBLICATION_MANIFEST.md): included material, excluded material, and claim boundary.
- [STATUS.md](STATUS.md): archive status.
- [CITATION.cff](CITATION.cff): citation metadata.

## Current Claim Boundary

The strongest public claim this repository supports is:

> In a small set of code-review prompt experiments, models produced materially different outputs when the same or similar task was framed as explanation, bug discovery, adversarial review, educational review, or persona-based review.

The notes support a hypothesis that prompt framing can activate different review behaviors. They do not prove a stable taxonomy of model cognition, a universal ranking of prompts, or a production calibration method.

## Main Observations

- Bug-discovery framing produced different outputs than explanation framing in the observed runs.
- Persona assignment appeared to increase confidence and reduce useful bug-finding signal in some observed runs.
- Some modes produced more false positives or less relevant output than others.
- Adversarial or skeptical framing appeared more useful for verification than for first-pass discovery.
- The public artifact does not include enough raw evidence to independently reproduce the exact counts or outcomes.

## What Is Not Included

Raw transcripts, scored result tables, tested source files, and scoring scripts are not included in this repository. Treat the results as preliminary case-study observations.

## Review Path

1. Read this README for the claim boundary.
2. Read [cognitive_modes.md](cognitive_modes.md) for the synthesis note.
3. Read [PUBLICATION_MANIFEST.md](PUBLICATION_MANIFEST.md) for data availability and excluded artifacts.

## Related Public Repositories

- [activation-gap](https://github.com/blazingRadar/activation-gap)
- [keeping-agents-honest](https://github.com/blazingRadar/keeping-agents-honest)

## Limitations

- Small sample.
- No raw transcript bundle in this public repo.
- No statistical replication.
- Model versions and behavior may have changed since April 2026.
- The mode labels are descriptive shorthand, not validated psychological categories.

## Author

Nick Cunningham  
<https://github.com/blazingRadar>  
<https://nickcunningham.io>
