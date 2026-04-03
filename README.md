# Know Your Model's Mode

**Research on cognitive activation patterns and persona degradation in frontier LLMs.**

---

## Abstract

This repository documents research into **Cognitive Modes** — the phenomenon where large language models adopt distinct behavioral and epistemic "modes" based on subtle prompt cues. 

The core discovery: **Persona assignment (e.g., "You are a senior engineer") measurably degrades auditing accuracy.** Uninstructed, "neutral" models consistently identify higher volumes of critical vulnerabilities than those constrained by specific professional personas. 

---

## Key Findings

- **The Persona Problem**: Role assignment inflates model confidence (locking it at 85–95%) while simultaneously adding noise and hiding real bugs.
- **Mode Taxonomy**: Empirically identified 10 modes (Audit, Knowledge, Adversarial, etc.) that models enter based on task framing.
- **The "Educational Mode" Disaster**: On Grok-3, instructing "Educational Mode" during a code audit caused the model to hallucinate more findings on a clean file than it found on a dirty one.
- **The Fox Prompt**: Proven situational framing consistently outperforms instructional framing by allowing models to select the appropriate cognitive mode naturally.

---

## Related Research

| Research | Link |
|---|---|
| **BUS (Hallucination Detection)** | [github.com/blazingRadar/sib29-gate](https://github.com/blazingRadar/sib29-gate) |
| **The Activation Gap** | [github.com/blazingRadar/activation-gap](https://github.com/blazingRadar/activation-gap) |
| **Keeping Agents Honest** | [github.com/blazingRadar/keeping-agents-honest](https://github.com/blazingRadar/keeping-agents-honest) |

---

## Implementation

The **Governor Pipeline** (a production implementation of these findings) provides real-time "mode-calibration" for security-critical AI assistants. For professional auditing inquiries, visit [nickcunningham.io](https://nickcunningham.io).

---

*Nick Cunningham — April 2026 — nickcunningham.io*
