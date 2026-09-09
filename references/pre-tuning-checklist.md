# Pre-tuning checklist

Use this checklist at the beginning of every new tuning project. Ask it in two phases so the user can start with business facts and provide infrastructure detail later.

## Phase A — required before proposing a tuning plan

Send the user the following grouped questions. Pre-fill known answers and ask them to correct anything wrong.

### 1. Goal and acceptance criteria

1. What problem are we solving: latency, throughput, concurrency, maximum input/context, cost, utilization, stability, or a combination?
2. Which goals are hard requirements, and which are preferences? Give target values where possible.
3. What must not regress: accuracy, tool calling, output format, reasoning quality, context capability, availability, or cost?
4. Is the objective single-request performance, total system capacity, user experience, or SLA under sustained load?

### 2. Model and artifact

1. Exact model name/version and artifact source or immutable revision?
2. Model type: dense/MoE, reasoning/non-reasoning, generative/embedding/reranking/multimodal/speech/diffusion?
3. Parameter scale, active parameters if MoE, precision/quantization, tokenizer/processor, and chat template?
4. Advertised and operational context/input limits? Expected output limit?
5. Required capabilities: streaming, reasoning, tools, JSON/schema, images, audio, video, adapters, or speculative decoding?

### 3. Business workload

1. Describe one real request and its expected correct output.
2. Online interactive, agentic, batch, or mixed?
3. Input size distribution: P50, P95, maximum, and units such as tokens, images, pixels, pages, frames, seconds, or documents.
4. Output size distribution and stop behavior.
5. Expected average, peak, and burst concurrency/QPS; open-loop arrival rate or closed-loop sessions?
6. Streaming or non-streaming? Multi-turn/session history? Thinking mode or effort?
7. Repeated prefixes/media/documents and expected cache hit ratio?
8. Tool calls, structured output, retrieval, preprocessing, or post-processing in the critical path?

### 4. Quality and benchmark

1. Which real dataset or representative samples may be used?
2. How is correctness decided: executable tests, labels, recall/precision, schema, judge, human review, or media metric?
3. Required benchmark tool, protocol, request format, sampling parameters, and random seed?
4. Required input × concurrency matrix, request count, repeats, and maximum test duration?
5. Should results represent cold, warm, cached, mixed, or steady-state production traffic?

### 5. Current symptom and comparison

1. What is slow or unstable, since when, and under which exact request shape?
2. Current measured values and formulas? Where are timestamps taken?
3. What changed around the regression: model, image, parameters, traffic, client, gateway, network, or data?
4. What is the comparison baseline, and is it the same model artifact, hardware, path, workload, and cache state?

At the end of Phase A, restate the answers, unknowns, proposed workload branch, and preliminary metric set. Do not propose fixed tuning values while critical items remain unknown.

## Phase B — required before changes or formal load

### 6. Platform and hardware

1. Cloud/on-prem platform, Region/AZ, service type, and deployment architecture?
2. Accelerator vendor/model/generation, memory per device, cards per node, node count, and whether resources are dedicated or shared?
3. Interconnect and network topology/bandwidth; storage type and model-loading path?
4. CPU, RAM, NUMA/container limits, and any preprocessing nodes?
5. Current replica count and parallelism topology: TP/DP/PP/EP/CP and PD separation if used?

### 7. Runtime and effective configuration

1. Inference framework/runtime, version, image tag and digest, driver/toolkit/firmware versions?
2. Deployment scripts/config files and the actual running process arguments/environment?
3. Current batching, sequence/concurrency, memory, cache, compile/graph, kernel, speculative, and communication settings?
4. Health checks, autoscaling, queueing, rate limits, timeout, retry, and fallback behavior?
5. Any warnings, unsupported flags, OOM, restarts, communication faults, empty responses, or serialization errors?

### 8. Request path and measurement point

1. Full path from client to model, including SDK/agent, authentication, gateway, load balancer, proxy, preprocessing, and model endpoint?
2. Can the direct model path and real application path both be tested?
3. Where should latency start/end, and what counts as first usable output?
4. Is streaming buffered or transformed anywhere? Are reasoning/tool deltas preserved?

### 9. Observability and access

1. Available metrics/logs/profilers for accelerator, memory, queues, per-rank requests, cache, communication, client, and gateway?
2. Is historical telemetry available for the regression window?
3. Which read-only systems may be inspected, and how will secrets be supplied without recording them?

### 10. Change authority and safety

1. May configuration be changed now? Which components are in scope?
2. Is restart/redeploy/scaling allowed, and what downtime window is approved?
3. Maximum load, cost, runtime, and resource limits for testing?
4. Required backup location, known-good version, rollback method, and rollback deadline?
5. Who approves the candidate for production, and which failures require immediate stop?

Before execution, show a confirmation block containing:

- confirmed scope and workload branch;
- metric formulas and hard gates;
- full matrix, total cells, repeats, and estimated duration;
- exact components/files to change;
- approved downtime/load/resource limits;
- backup and rollback target;
- unresolved risks.

Proceed only after the user confirms this block or has already provided equivalent explicit authorization.
