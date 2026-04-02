# Stop Telling Your Model It's a Senior Engineer

**Taxonomy and Empirical Evidence of Cognitive Mode Activation in Frontier LLMs**

This repository contains the research paper and data mapping 10 distinct "Cognitive Modes" in LLMs and proving why standard role-playing prompts (e.g., "You are a senior engineer") degrade performance in complex reasoning tasks.

## 📄 [Read the Paper: Cognitive Mode Activation](cognitive_modes.md)

### Key Findings
1. **The Persona Problem**: Role assignment inflates model confidence (locking it at 85-95%) while simultaneously adding noise and hiding real bugs.
2. **Mode Taxonomy**: Empirically identified 10 modes (Audit, Knowledge, Adversarial, etc.) that models enter based on task framing.
3. **The "Educational Mode" Disaster**: On Grok-3, instructing "Educational Mode" during a code audit caused the model to hallucinate *more* findings on a clean file than it found on a dirty one.
4. **The Fox Prompt**: Proven situational framing (the "Billy Poem") consistently outperforms instructional framing by allowing models to select the appropriate cognitive mode naturally.

### The 10 Modes Reference
| Mode | One-Line Description | Best For |
|---|---|---|
| **Audit** | Aggressive bug hunter. | Initial discovery on dirty files |
| **Knowledge** | Pattern retriever. Assumes correctness. | Understanding code |
| **Adversarial** | Competitive. Challenges claims. | Filtering false positives |
| **Critical** | Flags everything. High recall. | Stress testing |
| **Self-Aware** | Most calibrated. Transparent. | Verification |

---

### Data and Methodology
Research conducted March–April 2026 across GPT-4o, Claude Sonnet 3.5, and Grok-3.

All raw experiment logs and variances are available in the [SIB29 repository](https://github.com/blazingRadar/sib29).

**Author:** Nick Cunningham ([nickcunningham.io](https://nickcunningham.io))
