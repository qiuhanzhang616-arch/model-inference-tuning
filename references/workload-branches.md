# Workload branches

Select the branch from the user's actual work item. Combine branches only when the production request truly combines them.

## Interactive text or chat

- Primary metrics: time to first usable token, TPOT/inter-token latency, end-to-end latency, per-user responsiveness, aggregate token throughput, and success.
- Freeze system/chat template, session history, output length, streaming behavior, reasoning policy, and stop conditions.
- Test realistic multi-turn history and burst arrival, not only independent short prompts.

## Coding or tool-using agent

- Primary metrics: first reasoning/content token, TPOT, task completion time, tool-call correctness, iteration count, success/correctness, and recovery from tool errors.
- Use repository or sandbox tasks with executable verification. A token microbenchmark is necessary for engine analysis but insufficient for agent quality.
- Separate model time, tool execution, repository I/O, client orchestration, context construction, and gateway time.
- Preserve the actual reasoning/tool protocol. HTTP success with no usable content/tool call is failure.

## Long-context or RAG

- Primary metrics: TTFT, prefill throughput, retrieval latency, KV-cache capacity, cache hit rate, stable concurrency, correctness over distant evidence, and recovery.
- Stratify by actual token distribution and prefix reuse. Test cold and warm prefixes separately.
- Maximum context support is a functional boundary; it does not imply that maximum-context high concurrency is safe.

## Offline or batch generation

- Primary metrics: completed work items/s, total token throughput, makespan, utilization, failure/retry rate, and cost per successful item.
- Use open-loop arrival or a fixed backlog. TTFT may be secondary unless downstream stages depend on early output.
- Tune batching for sustained throughput while preserving output completeness and fairness constraints.

## Vision-language, document, or video understanding

- Primary units: requests/s plus images/pages/frames/video-seconds/s, end-to-end latency, preprocessing time, visual token count, memory, and task quality.
- Freeze resolution, image count, pages, frame sampling, codecs, media transfer format, OCR preprocessing, and structured-output schema.
- Separate media decode/preprocess, transfer, model prefill/decode, and post-processing.
- Repeated-media cache tests must not represent unique-document production traffic.

## Embedding or reranking

- Primary metrics: queries/s, documents/s, P50/P95/P99 latency, batch efficiency, maximum sequence length, memory, recall/nDCG/MRR, and cost.
- Freeze query/document length distributions and candidate counts. Do not report token-generation metrics.
- Compare dynamic versus fixed batching and include padding waste.

## Speech or audio

- Primary metrics: real-time factor, time to first partial result/audio, audio-seconds/s, end-to-end latency, word/error or task quality, and streaming stability.
- Freeze sample rate, channels, duration distribution, chunk size, VAD, language, and codec.
- Separate audio decoding, feature extraction, model inference, and synthesis/network playback.

## Image or video generation

- Primary metrics: time to first preview if supported, end-to-end generation latency, samples/s, pixels or frames/s, accelerator utilization, memory, failure rate, and quality metric/human review.
- Freeze resolution, duration, steps, sampler, guidance, seed policy, batch size, and safety/post-processing.
- Precision, quantization, step reduction, and resolution changes require separate quality series.

## Structured output or function calling

Apply this as a cross-cutting branch:

- Record schema validity, exact function/tool selection, argument validity, recovery behavior, and empty-output rate.
- Include tool definitions and parser version in the acceptance contract.
- Re-run after sustained load; startup smoke alone may miss graph, parser, or routing failures.

## Branch-specific parameter selection

After identifying the bottleneck, choose runtime-specific controls that express these general concepts:

- batch formation and maximum in-flight work;
- replica and tensor/data/pipeline/expert/context parallelism;
- memory fraction, KV or feature cache, offload, and input limits;
- graph capture/compile shapes and kernel implementations;
- speculative decoding or early-exit behavior;
- communication overlap, expert balancing, and collective algorithms;
- preprocessing workers, CPU affinity, I/O, and media cache;
- admission control, queue policy, timeout, retry, and streaming transport.

Parameter names and safe ranges must come from the exact model/runtime/hardware combination, not this generic branch guide.
