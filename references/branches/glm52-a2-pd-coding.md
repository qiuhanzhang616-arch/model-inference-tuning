# Example branch: GLM-5.2 W4A8C8 on Ascend A2, PD-separated coding

This branch records one historical project. Load it only when the current project materially matches GLM/GLM-Flash, Ascend A2, vLLM-Ascend/ModelArts, PD separation, or coding-agent diagnostics. Re-run the generic intake checklist and baseline; do not inherit these values.

## Historical project shape

- Streaming coding-agent workload with reasoning and tool calls.
- GLM-5.2 W4A8C8 on 64 Ascend A2 cards.
- Prefill DP4 × TP8 on 32 cards; Decode DP8 × TP4 on 32 cards.
- Retained-cycle model context: 256,000 tokens.
- Direct ModelArts and LiteLLM/application paths were measured separately.

## Retained C6 Decode changes

- `HCCL_BUFFSIZE`: 256 → 2560.
- `max-num-batched-tokens`: 8192 → 256.
- Enabled MLAPO.
- Removed Fused MC2 so shared-expert multistream overlap could remain active in that runtime.
- Removed an A3-only flag from the A2 configuration.
- Set the local host IP explicitly and changed `PYTHONHASHSEED` from 1234 to 0.
- Kept Prefill, DP8 × TP4 Decode topology, `gpu-memory-utilization=0.95`, weights, quantization, sampling policy, and the 256K context boundary unchanged.

Same-restart 8K comparisons showed approximately 10–11% lower TPOT and 11–15% higher output throughput in the tested cells. These are historical measurements, not targets for another deployment.

## Rejected branches

- Decode DP4 × TP8: lower 8K/8 performance and HTTP 200 empty streams in a 32K/8 stress cell.
- Fused MC2 + Dynamic EPLB: sustained load later produced GMM/tiling/AICore launcher errors.

The lesson is to test topology and kernel bundles under sustained load and after-stress tool calling; startup success is insufficient.

## GLM/A2-specific diagnosis checklist

- Inspect every Prefill and Decode process and every local DP-rank metrics port.
- Confirm configured flags were not disabled by startup-time conflicts.
- Separate request-level rank balancing from Dynamic EPLB expert balancing.
- Correlate per-rank request counts, TPOT, queue, preemption, HBM/KV use, and MTP acceptance.
- A/B supported MTP depths; low acceptance can make speculation slower.
- Treat FlashComm1, MLAPO, Fused MC2, Dynamic EPLB, sparse C8 kernels, graph capture sizes, and layer sharding as image/model/hardware-specific candidates.
- Never apply A3 environment variables or examples to A2 without exact runtime support evidence.
- For coding, measure first `reasoning_content` or content token, realistic decode length, tool-call success, executable correctness, and total agent completion time.

## Separate 1M-context experiment track

A later track explored `max-model-len=1048576`, Decode batching near 192 tokens, sequence capacity near 32, memory utilization near 0.92, MTP 5 versus 3, narrower graph captures, C8 reshape, and Prefill layer sharding. These were experiment inputs, not a universal winner. A 1M project must revalidate HBM/KV capacity, high-concurrency safety, quality, and recovery.

## Reusable operational lesson

In a later read-only sample, roughly 89% of Decode requests landed on the first two of eight ranks. Colder ranks had worse TPOT and lower MTP acceptance, while queues and preemptions were idle after the workload. This supported a routing/warmth/speculation hypothesis for burst degradation rather than a general HBM or service-outage conclusion.
