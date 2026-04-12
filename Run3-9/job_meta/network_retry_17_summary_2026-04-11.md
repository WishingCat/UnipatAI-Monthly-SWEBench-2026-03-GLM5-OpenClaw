# 2026-04-11 网络问题重跑 17 题总结

## 1. 范围与口径

本报告覆盖 `T:\AgentProjects\SWEBench\config\network_retry_2026-04-11.txt` 中的 17 道任务，即这次按“之前因网络问题未完成，现已修复网络后重新执行”的任务集。

最终结果口径说明：

- `manticoresoftware__manticoresearch_1a9cc54a_d4354257` 的最终有效结果取自首轮批次 `network_retry_openclaw_2026-04-11_12-35-18`，因为后续 16 题续跑时没有再重复运行它。
- 其余 16 题的最终有效结果取自修复 Docker 恢复逻辑后的完整批次 `network_retry_resilient_openclaw_2026-04-11_17-06-57`。
- `network_retry_resume_openclaw_2026-04-11_13-44-34` 这一轮全部是 `0 episode` 启动失败，用于确认 Docker 500 复发问题，不作为最终结果口径。
- `network_retry_smoke_nixopus_openclaw_2026-04-11_16-55-50` 是单题 smoke test，用于验证 Harbor + Docker + OpenClaw + GLM-5 已恢复到可进入多轮 LLM 对话的状态。

## 2. 总体结果

- 总任务数：`17`
- `PASS`（`reward=1.0`）：`2`
- 正常跑完但 `reward=0.0`：`6`
- 已进入多轮 LLM 对话但后续异常终止：`2`
- 未进入 LLM 对话、在环境/构建/安装阶段失败：`7`
- 成功进入多轮 LLM 对话的任务数：`10 / 17`
- 未进入任何 episode 的任务数：`7 / 17`
- 这 10 道进入对话的任务累计 episode 数：`909`
- 本批 episode 最多的任务：`stacklok__toolhive_1bf2f3ac_0642921f`，共 `281` 个 episode，最终因 `AgentTimeoutError` 结束

总体判断：

- Harbor + Docker + OpenClaw + `z-ai/glm-5` 这条链路本身是可用的，证据是有 `10` 道题成功进入了多轮 episode，其中 `2` 道拿到了 `reward=1.0`。
- 这批任务的主要瓶颈不是模型对话，而是 Windows + Docker Desktop Linux Engine 的稳定性，以及少数外部依赖/镜像源问题。

## 3. 时间线

1. `network_retry_openclaw_2026-04-11_12-35-18`
   - 开始于 `2026-04-11 12:35`
   - 首题 `manticoresoftware__manticoresearch_1a9cc54a_d4354257` 完整跑通，`reward=0.0`，`92` 个 episode
   - 随后 5 题因 Docker 500 等问题未能继续推进

2. `network_retry_resume_openclaw_2026-04-11_13-44-34`
   - 运行于 `2026-04-11 13:44 - 14:42`
   - 对剩余 16 题做过一次直接续跑
   - `16 / 16` 全部 `RuntimeError`
   - `0 / 16` 进入 LLM 对话

3. `network_retry_smoke_nixopus_openclaw_2026-04-11_16-55-50`
   - 先用 `nixopus__nixopus_309e6eae_0fb164cc` 做 smoke test
   - 成功生成 OpenClaw session，并进入多轮真实对话
   - 证明 Docker 恢复逻辑修复后，链路已重新具备可运行性

4. `network_retry_resilient_openclaw_2026-04-11_17-06-57`
   - 运行于 `2026-04-11 17:07 - 21:16`
   - 完整处理剩余 16 题
   - 最终产出：
     - `reward=1.0`：`2`
     - `reward=0.0`：`5`
     - `AgentTimeoutError`：`1`
     - `RewardFileNotFoundError`：`1`
     - `RuntimeError`：`7`

## 4. 逐题结果

| Task ID | 最终有效批次 | 最终结果 | Episode 数 | 是否进入多轮 LLM 对话 | 说明 |
| --- | --- | --- | ---: | --- | --- |
| `manticoresoftware__manticoresearch_1a9cc54a_d4354257` | `network_retry_openclaw_2026-04-11_12-35-18` | `reward=0.0` | 92 | 是 | 首题完整跑通，用于确认重跑链路可用 |
| `manticoresoftware__manticoresearch_27f5d482_a8159410` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `RuntimeError` | 0 | 否 | 多次 Docker Desktop API `500`，自动恢复后仍在 build/down 阶段失败 |
| `maximhq__bifrost_21cd30ba_3237e39f` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `reward=1.0` | 47 | 是 | 本批成功通过任务之一 |
| `memgraph__memgraph_0a8474dd_043c54ee` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `reward=0.0` | 99 | 是 | 已完整进入多轮对话并结束，但 verifier 结果为 0 |
| `microsoft__windowsappsdk_0038c232_d0eba8e0` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `RuntimeError` | 0 | 否 | Docker build 中 `wget packages.microsoft.com ...` 失败，退出码 `4` |
| `nixopus__nixopus_309e6eae_0fb164cc` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `reward=0.0` | 11 | 是 | 同时也是本批修复后的 smoke 验证题 |
| `openshift__microshift_b25417be_efa3e971` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `reward=0.0` | 83 | 是 | 对话链路和 verifier 均完整执行，但结果未通过 |
| `px4__px4-autopilot_b597227d_89855926` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `RuntimeError` | 0 | 否 | 基础镜像 `docker.m.daocloud.io/library/hb__d11_w12-heat-grid-convergence-reporter:latest` 返回 `403 Forbidden` |
| `runatlantis__atlantis_7920b36f_7758ed6c` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `reward=0.0` | 19 | 是 | 已正常进行多轮对话并完成 verifier |
| `specterops__bloodhound_078e3792_73acca3e` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `reward=1.0` | 62 | 是 | 本批成功通过任务之一 |
| `specterops__bloodhound_0a092be6_fbf55c9e` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `RuntimeError` | 0 | 否 | `agent setup` 失败，退出码 `1` |
| `stacklok__toolhive_1bf2f3ac_0642921f` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `AgentTimeoutError` | 281 | 是 | 进入大量 episode 后超时结束，是本批 episode 数最多的任务 |
| `stacklok__toolhive_261aeb2e_0108b113` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `reward=0.0` | 118 | 是 | 多轮对话和 verifier 完整执行，结果未通过 |
| `stacklok__toolhive_398eeec9_5e246db0` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `RewardFileNotFoundError` | 97 | 是 | 已完成大量对话，但 verifier 未产出 `reward.txt` / `reward.json` |
| `stackrox__stackrox_13efb8e1_66777350` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `RuntimeError` | 0 | 否 | Docker build 导出镜像时出现 `rpc error ... EOF` |
| `stackrox__stackrox_141b82d3_d510dffd` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `RuntimeError` | 0 | 否 | `go mod download` 过程中大量 `input/output error`，随后 Docker API `500` |
| `tektoncd__operator_09bfe1a0_790d5201` | `network_retry_resilient_openclaw_2026-04-11_17-06-57` | `RuntimeError` | 0 | 否 | Docker API `500`，自动恢复两次后仍未恢复健康 |

## 5. 主要失败模式

### 5.1 Docker Desktop Linux Engine 500 / 不健康

这是这批任务里最主要的系统性故障，直接影响到了以下任务：

- `manticoresoftware__manticoresearch_27f5d482_a8159410`
- `stackrox__stackrox_141b82d3_d510dffd`
- `tektoncd__operator_09bfe1a0_790d5201`

同时，以下任务虽然最终失败点不同，但也能看到 Docker 引擎不稳定的迹象：

- `stackrox__stackrox_13efb8e1_66777350`
- `stacklok__toolhive_398eeec9_5e246db0`

### 5.2 外部依赖或镜像源问题

- `microsoft__windowsappsdk_0038c232_d0eba8e0`
  - 失败点是 Dockerfile 内从微软软件源下载 PowerShell 依赖失败
- `px4__px4-autopilot_b597227d_89855926`
  - 失败点是基础镜像元数据拉取被 `403 Forbidden` 拒绝

### 5.3 Agent / Verifier 阶段异常

- `specterops__bloodhound_0a092be6_fbf55c9e`
  - `agent setup` 直接失败
- `stacklok__toolhive_1bf2f3ac_0642921f`
  - 成功进入大量 episode，但后续超时
- `stacklok__toolhive_398eeec9_5e246db0`
  - 对话过程正常，但 verifier 奖励文件缺失

## 6. 统计口径说明

- `stacklok__toolhive_1bf2f3ac_0642921f` 在 `result.json` 里被同时列入了 `reward=0.0` 和 `AgentTimeoutError`，这是 Harbor 本地记账上的不一致。
- 本报告对该任务采用 `episode_exports/summary.json` 和 `job.log` 的结果口径，即最终状态按 `AgentTimeoutError` 计算，而不是 `reward=0.0`。

## 7. 关键结论

- 这 17 道题里，真正证明“模型和 agent 多轮对话链路正常”的任务有 `10` 道，说明 OpenClaw + Harbor + 你的 GLM-5 接口在修复后是可用的。
- 当前最大阻塞项不是 LLM，而是 Docker Desktop Linux Engine 在大体量构建任务上的稳定性。
- 如果下一步继续推进，应优先分三类处理：
  - Docker 稳定性类：`manticoresearch_27f5...`、`stackrox_141b...`、`tektoncd__operator_09bf...`
  - 外部依赖源类：`windowsappsdk_0038...`、`px4__px4-autopilot_b597...`
  - Agent / verifier 类：`bloodhound_0a09...`、`stacklok__toolhive_1bf...`、`stacklok__toolhive_398e...`

## 8. 证据路径

- 任务清单：`T:\AgentProjects\SWEBench\config\network_retry_2026-04-11.txt`
- 首轮批次结果：`T:\AgentProjects\SWEBench\jobs\network_retry_openclaw_2026-04-11_12-35-18\result.json`
- 失败续跑结果：`T:\AgentProjects\SWEBench\jobs\network_retry_resume_openclaw_2026-04-11_13-44-34\result.json`
- 最终完整批次结果：`T:\AgentProjects\SWEBench\jobs\network_retry_resilient_openclaw_2026-04-11_17-06-57\result.json`
- 最终完整批次日志：`T:\AgentProjects\SWEBench\jobs\network_retry_resilient_openclaw_2026-04-11_17-06-57\job.log`
- smoke 验证任务目录：`T:\AgentProjects\SWEBench\jobs\network_retry_smoke_nixopus_openclaw_2026-04-11_16-55-50`
