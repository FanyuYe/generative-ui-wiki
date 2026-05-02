---
title: DeepSeek-V4
created: 2026-04-24
updated: 2026-04-24
type: system
tags: [artificial-intelligence, llm, model, system, training, inference, optimization]
sources: [raw/papers/deepseek-v4-technical-report-2026-04-24.md]
contradictions: []
status: research
---

# DeepSeek-V4

## Overview

DeepSeek-V4 is a preview open-model family from [[deepseek-ai]] built around the claim that frontier-class long-context reasoning should come from better efficiency, not only bigger dense attention budgets. The family includes a larger Pro model and a smaller Flash model, both designed for native one-million-token contexts.

## Architecture Notes

The model family retains DeepSeek's MoE backbone and Multi-Token Prediction lineage, then layers three main changes on top:

- Manifold-Constrained Hyper-Connections (mHC) for stronger residual pathways
- a hybrid attention stack that mixes CSA and HCA to reduce long-context inference cost
- the Muon optimizer as a major training component

The paper also treats implementation detail as part of the system design. FP4 quantization-aware training, fused kernels, contextual parallelism, heterogeneous KV caches, and on-disk KV reuse are presented as enabling parts of the model rather than optional engineering cleanup.

## Model Variants

- `DeepSeek-V4-Pro`: 1.6T total parameters, 49B activated
- `DeepSeek-V4-Flash`: 284B total parameters, 13B activated

The paper frames Pro as the stronger knowledge and agentic model, while Flash is optimized for lower-cost reasoning and long-context efficiency.

## Agent And Product Implications

DeepSeek-V4 is notable for product-facing behavior in addition to benchmark behavior:

- it supports multiple reasoning-effort modes
- it introduces a new XML-like tool-call schema
- it preserves reasoning traces across tool-calling sessions
- it includes quick-instruction tokens for search and routing side tasks
- it is trained and evaluated with a dedicated sandbox platform for agentic workloads

That combination makes V4 more than a raw base model. It is a model family explicitly shaped around coding, search, tool use, and long-horizon agent workflows.

## Evaluation Positioning

The paper positions DeepSeek-V4-Pro-Max as a release-time frontier open model: stronger than prior DeepSeek models, near top proprietary models on several reasoning and coding evaluations, and unusually strong on 1M-context tests. The paper also makes clear that V4 still trails the best closed models on some knowledge and agentic benchmarks.

## Limits

- The release is a preview, so reported capabilities should be treated as a moving target.
- Several headline comparisons are based on internal or partially internal evaluation harnesses.
- The architecture is intentionally complex; the authors explicitly say they want to simplify and distill it in future iterations.

## Related

- [[deepseek-ai]]
- [[deepseek-v4-technical-report|DeepSeek-V4: Towards Highly Efficient Million-Token Context Intelligence]]

## Sources

- raw/papers/deepseek-v4-technical-report-2026-04-24.md
