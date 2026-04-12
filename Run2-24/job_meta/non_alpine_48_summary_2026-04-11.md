# Harbor + OpenClaw 非 Alpine 48 题运行总结

生成时间：2026-04-11 10:01:01 +08:00

## 1. 汇报范围

本报告对应 Harbor `PR #879` + Docker + OpenClaw 架构，在本地 `dataset_non_alpine_pending` 任务集上的一次阶段性运行结果。

- 汇报对象：非 Alpine 任务 `48` 题
- 运行目录：`T:\AgentProjects\SWEBench\jobs\monthly_swebench_openclaw_non_alpine_2026-04-10_10-55-30`
- 任务已按要求停止，不再继续运行
- 模型接入：`z-ai/glm-5`，通过 OpenAI 兼容接口接入

补充说明：

- 另有 `c9s__bbgo_15f59ef4_85320527` 在这 48 题之外，已经因为连续 `3` 次启动失败且 `0 episode` 被提前记录为跳过，不计入本报告的 48 题统计
- 所有成功进入 agent 对话阶段的 trial，其 episode 内容均已落盘保存，可通过 `episode_exports` 目录查看

## 2. 总体结果

### 2.1 按任务状态统计

| 状态 | 数量 | 含义 |
| --- | ---: | --- |
| `PASS` | 6 | 正常完成，`reward = 1.0` |
| `COMPLETE_FAIL` | 15 | 正常完成，发生多轮 episode，但 `reward = 0.0` |
| `RAN_FAIL` | 3 | 已进入多轮 episode，但最终因超时等异常结束 |
| `STARTUP_FAIL` | 17 | 未进入 episode，在环境构建或 agent setup 阶段失败 |
| `STOPPED` | 1 | 因用户要求手动停止，未进入 episode |
| `NOT_STARTED` | 6 | 截止停止时尚未开始 |
| 合计 | 48 | 非 Alpine 任务全集 |

### 2.2 从“是否真正跑到 LLM 对话”角度看

- 真正进入多轮 LLM episode 的任务：`24` 题
- 其中成功解出：`6` 题
- 其中正常跑完但未解出：`15` 题
- 其中跑到一半超时失败：`3` 题

如果只看“已经进入多轮 agent 对话”的这 `24` 题：

- 任务级成功率：`25.0%`（`6 / 24`）

### 2.3 结论概览

- Harbor + OpenClaw + Docker 这条链路已经被验证为可运行，并且可以稳定地产生多 episode 对话
- 当前主要瓶颈不在模型调用，而在部分数据题目的环境构建、Docker 启动和 Go 依赖下载阶段
- 就已经真正跑起来的题目而言，模型具备完成部分 SWEBench 任务的能力，但仍有较多题目只能得到 `reward = 0.0`

## 3. 主要异常归类

### 3.1 启动阶段失败的 17 题

按根因可以大致分为以下几类：

| 异常类型 | 数量 | 典型表现 |
| --- | ---: | --- |
| Agent setup 失败 | 4 | `Agent setup failed with exit code 2` |
| Docker Desktop API 500 | 2 | `request returned 500 Internal Server Error` |
| Docker build/start 失败 | 5 | `docker compose build` 过程中失败 |
| Go 依赖下载失败 | 6 | `go mod download` 访问 `proxy.golang.org` 时 `EOF / unexpected EOF / connection refused` |

对应任务如下：

- Agent setup 失败：`manticoresoftware__manticoresearch_1a9cc54a_d4354257`、`memgraph__memgraph_0a8474dd_043c54ee`、`nixopus__nixopus_309e6eae_0fb164cc`、`openshift__microshift_b25417be_efa3e971`
- Docker Desktop API 500：`manticoresoftware__manticoresearch_27f5d482_a8159410`、`maximhq__bifrost_21cd30ba_3237e39f`
- Docker build/start 失败：`microsoft__windowsappsdk_0038c232_d0eba8e0`、`px4__px4-autopilot_b597227d_89855926`、`runatlantis__atlantis_7920b36f_7758ed6c`、`specterops__bloodhound_0a092be6_fbf55c9e`、`stacklok__toolhive_261aeb2e_0108b113`
- Go 依赖下载失败：`specterops__bloodhound_078e3792_73acca3e`、`stacklok__toolhive_1bf2f3ac_0642921f`、`stacklok__toolhive_398eeec9_5e246db0`、`stackrox__stackrox_13efb8e1_66777350`、`stackrox__stackrox_141b82d3_d510dffd`、`tektoncd__operator_09bfe1a0_790d5201`

### 3.2 跑起来但最终失败的 3 题

这 3 题都已经产生了大量 episode，但最终因为 agent 执行超时结束：

- `bytedance__sonic_28040bdd_c9e5b0f6`：`178` episodes，`AgentTimeoutError`
- `ggml-org__llama.cpp_212f4521_990e4d96`：`216` episodes，`AgentTimeoutError`
- `protocolbuffers__protobuf_08244d7a_ab14c0f8`：`186` episodes，`AgentTimeoutError`

这类题说明环境和模型对话本身是通的，但在限定时间内没有完成任务。

## 4. 逐题结果明细

说明：

- `Trial` 为 Harbor 生成的实际运行目录名
- `Episodes` 为本地已导出的 episode 数量
- `Reward` 来自 verifier
- `状态说明` 用于概括该题最终落点

### 4.1 成功通过：6 题

| 任务 | Trial | Episodes | Reward | 状态说明 |
| --- | --- | ---: | ---: | --- |
| `lynx-family__lynx_5a49d2c1_17bee8a0` | `lynx-family__lynx_5a49d2c1_17bee__gULwKMg` | 123 | 1.0 | 正常完成并通过 |
| `maaxyz__maaframework_170af58b_e78524c8` | `maaxyz__maaframework_170af58b_e7__Qc7qeJJ` | 143 | 1.0 | 正常完成并通过 |
| `open-policy-agent__opa_1897f8c2_1ac64ef1` | `open-policy-agent__opa_1897f8c2___J6TSoZQ` | 95 | 1.0 | 正常完成并通过 |
| `openthread__openthread_1f218fd0_f1794af0` | `openthread__openthread_1f218fd0___QocarDH` | 112 | 1.0 | 正常完成并通过 |
| `rancher__fleet_36d9b971_c1848e0d` | `rancher__fleet_36d9b971_c1848e0d__kBdhHb5` | 98 | 1.0 | 正常完成并通过 |
| `runatlantis__atlantis_0ad6fc00_1ff1c1d1` | `runatlantis__atlantis_0ad6fc00_1__x5Y5iqt` | 182 | 1.0 | 正常完成并通过 |

### 4.2 正常跑完但未通过：15 题

| 任务 | Trial | Episodes | Reward | 状态说明 |
| --- | --- | ---: | ---: | --- |
| `bytedance__sonic_9a1c1488_f8ba977b` | `bytedance__sonic_9a1c1488_f8ba97__tZgicgp` | 82 | 0.0 | 正常完成，但 verifier 未通过 |
| `maaxyz__maaframework_43749f2b_803c78ea` | `maaxyz__maaframework_43749f2b_80__9wrKUk9` | 25 | 0.0 | 正常完成，但 verifier 未通过 |
| `microsoft__retina_5e7be1fd_c6e26519` | `microsoft__retina_5e7be1fd_c6e26__oDBDDVz` | 87 | 0.0 | 正常完成，但 verifier 未通过 |
| `nextlevelbuilder__goclaw_0881d7fc_7fa2d201` | `nextlevelbuilder__goclaw_0881d7f__VwB3rNo` | 112 | 0.0 | 正常完成，但 verifier 未通过 |
| `nextlevelbuilder__goclaw_419935b9_3cbdd8d7` | `nextlevelbuilder__goclaw_419935b__Qj6emWv` | 510 | 0.0 | 正常完成，但 verifier 未通过 |
| `nixopus__nixopus_58967754_119995e5` | `nixopus__nixopus_58967754_119995__YegWkLR` | 8 | 0.0 | 正常完成，但 verifier 未通过 |
| `open-policy-agent__opa_5e57a275_e0d66617` | `open-policy-agent__opa_5e57a275___abz2kZR` | 123 | 0.0 | 正常完成，但 verifier 未通过 |
| `projectdiscovery__nuclei_17e9f429_bde02720` | `projectdiscovery__nuclei_17e9f42__duUDia9` | 139 | 0.0 | 正常完成，但 verifier 未通过 |
| `rancher__rancher_1757a541_471379ae` | `rancher__rancher_1757a541_471379__fr3kXwQ` | 7 | 0.0 | 正常完成，但 verifier 未通过 |
| `rancher__rancher_1ed0b488_7be51434` | `rancher__rancher_1ed0b488_7be514__dnNQkUE` | 133 | 0.0 | 正常完成，但 verifier 未通过 |
| `rudrankriyam__app-store-connect-cli_066f0ed0_34f7484a` | `rudrankriyam__app-store-connect-__cQhzUFu` | 51 | 0.0 | 正常完成，但 verifier 未通过 |
| `rudrankriyam__app-store-connect-cli_0fe62cdf_5ef4ffae` | `rudrankriyam__app-store-connect-__zqmJgbu` | 89 | 0.0 | 正常完成，但 verifier 未通过 |
| `runatlantis__atlantis_2fd36a9e_df9d6b7a` | `runatlantis__atlantis_2fd36a9e_d__nMoA5bt` | 40 | 0.0 | 正常完成，但 verifier 未通过 |
| `seaweedfs__seaweedfs_156e1a6e_ecadeddc` | `seaweedfs__seaweedfs_156e1a6e_ec__YFQq47u` | 78 | 0.0 | 正常完成，但 verifier 未通过 |
| `smartcontractkit__chainlink_10b486b7_61c19e70` | `smartcontractkit__chainlink_10b4__eYnKtYE` | 69 | 0.0 | 正常完成，但 verifier 未通过 |

### 4.3 已进入多轮对话，但最终异常结束：3 题

| 任务 | Trial | Episodes | Reward | 状态说明 |
| --- | --- | ---: | ---: | --- |
| `bytedance__sonic_28040bdd_c9e5b0f6` | `bytedance__sonic_28040bdd_c9e5b0__uRNViWo` | 178 | 0.0 | 运行中发生 `AgentTimeoutError` |
| `ggml-org__llama.cpp_212f4521_990e4d96` | `ggml-org__llama.cpp_212f4521_990__fpb8kRN` | 216 | 0.0 | 运行中发生 `AgentTimeoutError` |
| `protocolbuffers__protobuf_08244d7a_ab14c0f8` | `protocolbuffers__protobuf_08244d__V3ZmdKi` | 186 | 0.0 | 运行中发生 `AgentTimeoutError` |

### 4.4 启动阶段失败：17 题

| 任务 | Trial | Episodes | 状态说明 |
| --- | --- | ---: | --- |
| `manticoresoftware__manticoresearch_1a9cc54a_d4354257` | `manticoresoftware__manticoresear__v5JJux7` | 0 | Agent setup 失败，退出码 2 |
| `manticoresoftware__manticoresearch_27f5d482_a8159410` | `manticoresoftware__manticoresear__Cwy6ZQW` | 0 | Docker build 阶段触发 Docker Desktop API 500 |
| `maximhq__bifrost_21cd30ba_3237e39f` | `maximhq__bifrost_21cd30ba_3237e3__z3EUZFj` | 0 | Docker build 阶段触发 Docker Desktop API 500 |
| `memgraph__memgraph_0a8474dd_043c54ee` | `memgraph__memgraph_0a8474dd_043c__2z4gQdC` | 0 | Agent setup 失败，退出码 2 |
| `microsoft__windowsappsdk_0038c232_d0eba8e0` | `microsoft__windowsappsdk_0038c23__kVz5MWR` | 0 | Docker build 失败 |
| `nixopus__nixopus_309e6eae_0fb164cc` | `nixopus__nixopus_309e6eae_0fb164__AqDPnPt` | 0 | Agent setup 失败，退出码 2 |
| `openshift__microshift_b25417be_efa3e971` | `openshift__microshift_b25417be_e__WhnNX5N` | 0 | Agent setup 失败，退出码 2 |
| `px4__px4-autopilot_b597227d_89855926` | `px4__px4-autopilot_b597227d_8985__wpSGUz3` | 0 | Docker build/start 失败 |
| `runatlantis__atlantis_7920b36f_7758ed6c` | `runatlantis__atlantis_7920b36f_7__jwt98ST` | 0 | Docker build/start 失败 |
| `specterops__bloodhound_078e3792_73acca3e` | `specterops__bloodhound_078e3792___uag9cg2` | 0 | `go mod download` 失败，访问 `proxy.golang.org` 异常 |
| `specterops__bloodhound_0a092be6_fbf55c9e` | `specterops__bloodhound_0a092be6___F3NrLsq` | 0 | Docker build/start 失败 |
| `stacklok__toolhive_1bf2f3ac_0642921f` | `stacklok__toolhive_1bf2f3ac_0642__iy7mGcM` | 0 | `go mod download` 失败，访问 `proxy.golang.org` 异常 |
| `stacklok__toolhive_261aeb2e_0108b113` | `stacklok__toolhive_261aeb2e_0108__rrauXhc` | 0 | Docker build/start 失败 |
| `stacklok__toolhive_398eeec9_5e246db0` | `stacklok__toolhive_398eeec9_5e24__YUGeuyF` | 0 | `go mod download` 失败，访问 `proxy.golang.org` 异常 |
| `stackrox__stackrox_13efb8e1_66777350` | `stackrox__stackrox_13efb8e1_6677__mi3GarN` | 0 | `go mod download` 失败，访问 `proxy.golang.org` 异常 |
| `stackrox__stackrox_141b82d3_d510dffd` | `stackrox__stackrox_141b82d3_d510__cB6YbQz` | 0 | `go mod download` 失败，访问 `proxy.golang.org` 异常 |
| `tektoncd__operator_09bfe1a0_790d5201` | `tektoncd__operator_09bfe1a0_790d__m8nUZ8N` | 0 | `go mod download` 失败，访问 `proxy.golang.org` 异常 |

### 4.5 用户要求手动停止：1 题

| 任务 | Trial | Episodes | 状态说明 |
| --- | --- | ---: | --- |
| `tektoncd__operator_4f93b095_9e20b9ca` | `tektoncd__operator_4f93b095_9e20__aVpbX6T` | 0 | 已创建 trial，但在真正启动前按用户要求停止 |

### 4.6 截止停止时尚未开始：6 题

| 任务 | 状态说明 |
| --- | --- |
| `tektoncd__operator_790d5201_cc10d0d8` | 未开始 |
| `tencent__tgfx_48cd318f_4638abd6` | 未开始 |
| `tencent__weknora_463f40fa_380f5d94` | 未开始 |
| `tencent__weknora_9d2228a8_b291381c` | 未开始 |
| `tikv__pd_161d5718_033398bf` | 未开始 |
| `tikv__pd_1bd15b06_99eb5b50` | 未开始 |

## 5. episode 与日志落盘位置

本批次的重要索引和日志如下：

- 总 job 目录：`T:\AgentProjects\SWEBench\jobs\monthly_swebench_openclaw_non_alpine_2026-04-10_10-55-30`
- episode 索引：`T:\AgentProjects\SWEBench\jobs\monthly_swebench_openclaw_non_alpine_2026-04-10_10-55-30\episode_export_index.json`
- 汇总结果：`T:\AgentProjects\SWEBench\jobs\monthly_swebench_openclaw_non_alpine_2026-04-10_10-55-30\result.json`
- 监督状态：`T:\AgentProjects\SWEBench\jobs\monthly_swebench_openclaw_non_alpine_2026-04-10_10-55-30\supervisor_status.json`
- 启动失败登记：`T:\AgentProjects\SWEBench\jobs\monthly_swebench_openclaw_non_alpine_2026-04-10_10-55-30\startup_failure_registry.json`
- 已跳过任务记录：`T:\AgentProjects\SWEBench\jobs\monthly_swebench_openclaw_non_alpine_2026-04-10_10-55-30\skipped_startup_issues.jsonl`

单题对话与轨迹文件位置遵循统一结构：

- `T:\AgentProjects\SWEBench\jobs\<trial_name>\episode_exports\summary.json`
- `T:\AgentProjects\SWEBench\jobs\<trial_name>\episode_exports\episodes.jsonl`
- `T:\AgentProjects\SWEBench\jobs\<trial_name>\agent\trajectory.json`
- `T:\AgentProjects\SWEBench\jobs\<trial_name>\agent\openclaw-session.jsonl`（若该 trial 产生了会话流式日志）

## 6. 最终判断

这 48 题的阶段性结论如下：

- Harbor + Docker + OpenClaw + 当前模型接入已经具备批量运行 SWEBench 任务的能力
- 真正进入多轮 LLM 对话的 24 题中，有 6 题成功通过 verifier
- 目前最值得优先继续优化的不是 agent 框架本身，而是启动阶段的环境稳定性，尤其是：
  - Go 依赖下载链路
  - Docker Desktop API 500 问题
  - 少数任务的 agent setup 失败
- 如果后续继续补跑，建议优先针对启动失败的 17 题做网络与环境层面的专项修复，而不是直接重复全量重试
