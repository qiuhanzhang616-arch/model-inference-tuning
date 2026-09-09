# 负载与指标分支

## 在线文本/对话

- 重点：首个可用 Token、TPOT/ITL、端到端时延、会话体验、输出吞吐和成功率。
- 固定多轮历史、Reasoning、Streaming、输出长度和 Stop。
- 同时测试平稳与突发流量。

## Coding 或 Tool-using Agent

- 重点：首个 Reasoning/Content Token、TPOT、任务总时长、工具正确率、迭代次数、任务成功和可执行正确率。
- Token 微基准用于定位引擎，但不能替代真实仓库/Sandbox 任务。
- 分开记录模型、工具、代码仓 I/O、Context 构造、Agent 编排和网关时间。

## 长上下文/RAG

- 重点：TTFT、Prefill 吞吐、检索时延、KV/缓存容量、命中率、稳定并发、远距离证据正确率和恢复。
- 按实际 Token 分布和前缀复用分层，冷/热结果分开。
- 最大上下文可用不等于最大上下文下可以高并发。

## 离线批处理

- 重点：work items/s、Token 或媒体吞吐、总 Makespan、利用率、失败/重试和单位成本。
- 使用固定积压或 Open-loop 到达；不能只统计成功子集。

## 视觉语言、文档或视频理解

- 重点：requests/s、images/pages/frames/video-seconds/s、预处理、视觉 Token、端到端时延、内存和质量。
- 固定图片数、分辨率、页数、帧采样、Codec、媒体传输、OCR 和输出 Schema。
- 唯一文档流量不能用重复媒体缓存结果代表。

## Embedding/重排

- 重点：queries/s、documents/s、P50/P95/P99、Padding 浪费、最大序列、内存、Recall/nDCG/MRR 和成本。
- 不使用生成模型的 TTFT/TPOT 口径。

## 语音

- 重点：Real-time Factor、首个 Partial/Audio、audio-seconds/s、端到端时延、WER/任务质量和 Streaming 稳定性。
- 固定采样率、声道、时长、Chunk、VAD、语言和 Codec。

## 图像/视频生成

- 重点：首个预览、生成时延、samples/s、pixels/frames/s、利用率、内存、失败和质量。
- 固定分辨率、时长、Steps、Sampler、Guidance、Seed 和后处理。

## 结构化输出/Function Calling

- 作为横向分支，记录 Schema 有效率、工具选择、参数正确率、恢复行为和空响应。
- 压力后再次验证，避免只通过启动冒烟。

参数名和范围必须来自当前模型、运行时与硬件。各分支只定义问题和指标，不提供固定最佳值。
