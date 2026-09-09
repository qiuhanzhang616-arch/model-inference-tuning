---
name: model-inference-tuning
description: Design, execute, and validate reproducible inference-performance tuning for different models, accelerators, frameworks, deployment platforms, and workloads. Use when users want to improve latency, throughput, concurrency, memory or accelerator utilization, context capacity, cost, stability, or quality under load. Covers online and batch text generation, coding agents, long-context/RAG, multimodal and document models, embeddings/rerankers, speech, and image/video generation; do not reuse historical parameter values without a new project baseline.
---

# Model Inference Tuning

Find the best **validated configuration for the current project**, not a universal parameter recipe. Model architecture, artifact, precision, hardware, runtime, request shape, quality target, and traffic pattern define a new experiment domain each time.

## Mandatory first response

Whenever a user asks to start, plan, continue, or repeat tuning for a new project, first read [pre-tuning-checklist.md](references/pre-tuning-checklist.md).

1. Ask the Phase A questions before proposing parameters or a formal benchmark matrix.
2. Ask the Phase B questions before changing configuration, restarting a service, scaling resources, or sending material load.
3. Pre-fill answers already stated by the user or available in supplied files, but show them for confirmation. Mark unavailable facts as `unknown`; never invent them.
4. Let the user answer in free text or by filling the checklist. Do not force the user to know implementation details that can be discovered read-only.
5. If only a report, historical summary, or offline test harness is requested, complete that scoped work without treating missing production access as a blocker.

Do not prescribe GLM-, vLLM-, CUDA-, Ascend-, ModelArts-, or coding-specific settings during intake unless the current project actually uses them.

## Route to the workload branch

After intake, read only the applicable section of [workload-branches.md](references/workload-branches.md). A project may combine branches, but each branch must retain its own workload shape and quality gate.

- Interactive text/chat
- Coding or tool-using agent
- Long-context or RAG
- Offline/batch generation
- Vision-language, document, or video understanding
- Embedding or reranking
- Speech/audio
- Image/video generation
- Structured output or function calling

For the historical GLM-5.2 W4A8C8, Ascend A2, ModelArts, PD-separated coding case, read [branches/glm52-a2-pd-coding.md](references/branches/glm52-a2-pd-coding.md). It is an example branch, not the default path.

## Freeze the acceptance contract

Before benchmarking, write a manifest containing:

- confirmed model artifact, precision, runtime, hardware, topology, and traffic path;
- input/output distributions and full workload matrix;
- metric formulas and measurement point;
- cold, warm, cached, and steady-state definitions;
- quality, correctness, stability, capacity, and cost gates;
- allowed changes, downtime, test duration, stop conditions, and rollback target.

Expand every requested dimension into the complete Cartesian matrix and state total cells, repeats, and estimated duration. If the matrix is too expensive, ask the user to approve a staged screening design; do not silently omit cells.

## Establish a trustworthy baseline

Create a four-way fact ledger:

1. Intended configuration.
2. Saved deployment files or UI values.
3. Actual process arguments, environment, loaded artifact, and runtime logs.
4. Observed metrics under the frozen workload.

Resolve mismatches before tuning. A file name, release label, service version, or successful startup does not prove which parameters are effective.

Benchmark the shortest direct model path and the real application path with identical requests when possible. This separates model execution from client, preprocessing, authentication, gateway, network, queueing, retry, and post-processing delay.

Collect per-instance and per-rank metrics rather than one aggregate whenever the runtime is distributed. Verify actual accelerator count, visibility, topology, memory, health, queue state, restarts, and error logs.

## Choose metrics for the branch

Do not force token metrics onto non-token workloads.

Common metrics include:

- Latency: end-to-end P50/P95/P99/max, queue time, service time, and time to first usable output.
- Throughput: requests/s, tokens/s, images/s, pages/s, frames/s, audio-seconds/s, or samples/s.
- Capacity: stable concurrency/QPS, maximum input/output shape, memory headroom, and queue recovery.
- Efficiency: accelerator utilization, memory bandwidth/capacity, communication, CPU, network, storage, power, and cost per successful work item.
- Reliability: success, timeout, retry, empty/invalid response, restart, OOM, preemption, and recovery time.
- Quality: task-specific correctness, recall/precision, schema validity, tool accuracy, semantic quality, or media-quality score.

For streaming reasoning models, count the first `reasoning_content` or equivalent model delta as first output when measuring TTFT. Keep per-request output rate separate from aggregate system throughput. Include failed requests and retry time in SLA calculations.

## Form tuning hypotheses

Use observed bottlenecks to choose candidates. Typical tuning families are:

1. Request formation and preprocessing.
2. Admission control, batching, scheduling, and queueing.
3. Parallelism and replica/node topology.
4. Context/shape limits, cache strategy, and memory allocation.
5. Kernel, graph/compile, attention, speculative decoding, and communication features.
6. Precision or quantization, only with an explicit quality gate.
7. Client, gateway, transport, retry, timeout, and streaming behavior.

Change one independent variable at a time, or identify an inseparable bundle and explain it. Define the expected metric direction, failure mode, and rollback condition before execution. Hardware- or model-generation-specific flags require support evidence from the exact runtime/image.

Do not improve a result by quietly reducing input size, output length, reasoning work, context capability, quality threshold, unique-data ratio, or failure accounting. Those are product tradeoffs and need explicit approval plus a separate result series.

## Apply and test safely

Before an approved mutation:

- back up exact live files/artifacts and record hashes;
- record service/deployment/image identifiers and full effective runtime;
- prepare an executable rollback independent of the candidate files;
- confirm the user-authorized downtime, load, and resource scope.

Execute in stages:

1. Readiness and functional smoke.
2. Warm-up/compile characterization.
3. Small performance screening.
4. Same-condition baseline/candidate A/B.
5. Full matrix and quality suite.
6. Boundary, sustained-load, and recovery tests.
7. Post-stress functional and health regression.

Label cold, warm, cached, mixed, and steady-state results separately. Repeat material wins. Stop new load and roll back on agreed hard failures such as OOM, process death, invalid/empty output, unacceptable quality loss, communication/kernel errors, runaway retries, or failure to recover.

## Select and report

Call a candidate “best” only among the tested configurations and only if it satisfies all hard gates. Report:

- intake answers and remaining unknowns;
- acceptance contract and exact metric definitions;
- baseline fact ledger;
- candidate diffs and hypotheses;
- raw and summarized results, including failures;
- resource and bottleneck evidence;
- quality/stability comparison;
- accepted and rejected candidates with reasons;
- current running state and rollback procedure;
- measured conclusions, reasonable inferences, and untested recommendations as separate categories.

Keep secrets out of artifacts. Historical scores and parameters may illustrate method but must never become another project's baseline or promise.
