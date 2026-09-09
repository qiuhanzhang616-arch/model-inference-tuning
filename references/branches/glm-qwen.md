# GLM/Qwen 调优分支

只在当前模型属于 GLM/GLM-Flash/CodeGeeX 或 Qwen/Qwen-Coder/Qwen-VL 等系列时加载。本文件不提供跨项目默认参数。

## 先确认

- 精确模型 Revision、Dense/MoE、激活参数、Reasoning、MTP/草稿能力和量化格式。
- 当前硬件代际、运行时镜像及其明确支持的 Kernel/通信优化。
- Chat Template、Reasoning Parser、Tool Parser 和客户端字段是否一致。
- 实际进程中的上下文、批处理、序列数、内存、并行拓扑、缓存和图编译参数。

## GLM 分支

- Streaming TTFT 计入首个 Reasoning Token，同时另报首个 Content Token（若业务需要）。
- Coding/Agent 场景重点看 Decode TPOT、MTP 接受率、Tool Calling、空流和任务完成时间；长上下文同时看 Prefill/KV。
- MLAPO、FlashComm、Fused MC2、Shared Expert Multistream、Dynamic EPLB、稀疏 C8、MTP 和 PD 只在当前模型/镜像/硬件组合中 A/B。
- Request Load Balancing 与 Dynamic EPLB 分别解决 Rank 请求分配和 MoE Expert 负载，不能混为同一功能。
- 检查所有 DP Rank/本地端口；平均值可能隐藏冷热 Rank 和 MTP 接受率差异。

### 历史 A2 PD Coding 案例

一个 64 卡 A2、GLM-5.2 W4A8C8、Prefill DP4×TP8、Decode DP8×TP4 项目中，保留候选曾将 Decode Batch Token 从 8192 降到 256、增大 HCCL Buffer、启用 MLAPO、解除 Fused MC2 与 Shared Expert Multistream 冲突，并移除 A3 专用参数。相同重启周期的特定 8K 测试约改善 10%–11% TPOT、提高 11%–15% 输出吞吐。

这些数字只说明该项目的实验方法。后续 Decode DP4×TP8 因空流/性能被否决；Fused MC2 + Dynamic EPLB 因持续负载下 GMM/Tiling 错误被否决。不得复制为新项目默认值。

## Qwen 分支

- 区分文本、Coder、MoE、VL、Audio 等模型；它们的 Processor、工作量和指标不同。
- 验证 Thinking、Tool Calling、结构化输出和 Streaming 字段在目标客户端中的实际表现。
- Qwen-VL 将媒体预处理、视觉 Token、Processor 缓存和文本 Decode 分段测量。
- MoE 版本重点观察 Expert/Rank 不均衡、通信、长尾和 Kernel 支持；Dense 版本不套用 EPLB 假设。

## 分支晋升门槛

- 性能收益在相同缓存/路径/输出长度下可复测。
- Tool/JSON/Reasoning/多模态等目标能力不退化。
- 无新增空响应、OOM、通信、图编译/Kernel、实例重启或恢复失败。
