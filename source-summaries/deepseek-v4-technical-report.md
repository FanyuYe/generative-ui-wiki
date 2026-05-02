---
title: DeepSeek-V4 Towards Highly Efficient Million-Token Context Intelligence
created: 2026-04-24
updated: 2026-04-24
type: paper-summary
tags: [artificial-intelligence, llm, deep-learning, academic-research, paper, training, inference, optimization]
sources: [raw/papers/deepseek-v4-technical-report-2026-04-24.md]
contradictions: []
authors: [DeepSeek-AI]
venue: "Technical report"
year: 2026
status: read
---

# DeepSeek-V4: Towards Highly Efficient Million-Token Context Intelligence

## Summary

This paper introduces the preview DeepSeek-V4 family: DeepSeek-V4-Pro with 1.6T total parameters and 49B activated parameters, and DeepSeek-V4-Flash with 284B total parameters and 13B activated parameters. The central claim is that DeepSeek can make one-million-token context windows practical for open models by reducing long-context inference cost instead of scaling context with conventional dense attention.

## Key Contributions

- Hybrid attention combines Compressed Sparse Attention (CSA) and Heavily Compressed Attention (HCA) so the model can shrink long-context compute and KV-cache costs without giving up million-token support.
- Manifold-Constrained Hyper-Connections (mHC) replace standard residual mixing with a more structured residual pathway intended to improve signal propagation and training stability.
- Muon is adopted as the main optimizer for most modules, alongside additional training-stability techniques such as SwiGLU clamping and Anticipatory Routing.
- The paper treats systems work as first-class model work: fused MoE kernels, TileLang kernels, FP4 quantization-aware training, contextual parallelism, on-disk KV cache storage, and a large-scale sandbox stack are all part of the claimed capability jump.
- Post-training shifts from the mixed RL stage used in DeepSeek-V3.2 toward a specialist-training-plus-on-policy-distillation pipeline. Domain experts are trained independently, then merged into one unified student model.

## Architecture And Training Notes

DeepSeek-V4 keeps the DeepSeekMoE and Multi-Token Prediction lineage from earlier DeepSeek releases, but changes the long-context economics substantially. In the paper's one-million-token setting, DeepSeek-V4-Pro is reported to require 27% of the single-token inference FLOPs and 10% of the KV cache of DeepSeek-V3.2. DeepSeek-V4-Flash is presented as even more efficient, at 10% of the FLOPs and 7% of the KV cache.

The pre-training run is also notable for scale and infrastructure coupling. The report says DeepSeek-V4-Flash was trained on 32T tokens and DeepSeek-V4-Pro on 33T tokens. It also describes FP4 expert weights, heterogeneous KV cache structures with on-disk reuse, and training/inference infrastructure designed explicitly around long-horizon and agentic workloads.

## Post-Training And Agent Notes

The paper is not only about raw model architecture. It also adds several product-facing behaviors that matter for AI-native software:

- reasoning modes for `Non-think`, `Think High`, and `Think Max`
- XML-like tool-call formatting built around the `|DSML|` token family
- interleaved thinking that preserves reasoning traces across tool-calling sessions
- quick-instruction tokens for side tasks such as search-query generation, authority classification, domain classification, and URL reading decisions
- DSec, a production sandbox platform for agentic training and evaluation

These details make the source relevant to both fundamentals and software-engineering lanes in this wiki, not just model benchmarking.

## Evaluation Takeaways

- On the paper's reported numbers, DeepSeek-V4-Pro-Max is positioned as the strongest open model in the DeepSeek line and close to top proprietary systems on several reasoning and coding tasks.
- The paper claims especially strong long-context behavior: DeepSeek-V4-Pro-Max beats Gemini-3.1-Pro on the reported MRCR and CorpusQA 1M evaluations, though it still trails Claude Opus 4.6 on MRCR.
- Agentic results are strong but not dominant. On several public code-agent and tool-use evaluations, DeepSeek-V4-Pro is competitive with leading open models and still behind the best closed models on some tasks.
- Flash is presented as the cheaper reasoning-oriented sibling: lower knowledge retention than Pro, but often comparable on reasoning when given a larger thinking budget.

## Durable Insights

- Long-context capability is increasingly a systems-and-architecture problem, not only a pre-training-scale problem.
- Open-model competition is moving toward integrated stacks where optimizer choice, attention design, quantization, kernel work, cache management, and sandbox infrastructure all materially shape headline model performance.
- Post-training is becoming more modular: specialist models plus distillation are treated as a more reliable route than one monolithic mixed-RL stage.

## Time-Sensitive Or Uncertain Claims

- Many comparison numbers are internal or partially internal evaluations and should be treated as release-time claims rather than final consensus.
- The paper is explicitly a preview release, so configuration details, benchmark standings, and deployment characteristics may shift quickly.

## Connections

- [[deepseek-v4]] is the durable system page for the model family described here.
- [[deepseek-ai]] is the entity page for the lab behind the DeepSeek model line and infrastructure stack.

## Open Questions

- How much of the reported million-token efficiency gain comes from the CSA/HCA design itself versus the surrounding systems stack?
- Which parts of the post-training recipe transfer well to other open-model families?
- How stable are the strongest benchmark claims under independent external evaluation and production agent workloads?

## Sources

- raw/papers/deepseek-v4-technical-report-2026-04-24.md
