# 第一次运行

本目录整理自这次 Harbor 批量运行作业：

- 源作业目录：`T:\AgentProjects\20260408SWEBench\harbor_jobs\monthly-swebench-openclaw-full112-glm5-20260409-0057`

## 本次运行概况

- 本次批量计划运行 `112` 个 task
- 实际成功落盘了 `63` 个 task 的 Harbor 运行记录

这 `63` 个 task 中，已整理到本目录的有以下三类：

- `passed_13`
  - 通过题，共 `13` 个
- `finished_reward0_26`
  - 正常结束但未通过 verifier，共 `26` 个
- `agent_timeout_4`
  - `AgentTimeoutError` 的题目，共 `4` 个

以上三类一共整理了 `43` 个 task 的运行记录，便于单独查看与复盘。

## 其他未整理进来的 task

其余 task 属于异常结束，未能成功运行完成。

这些异常里，以 VPN / Docker / Docker Hub 相关网络错误为主，也包含少量：

- 容器环境兼容问题
- agent setup 失败
- 作业尾部取消错误

## 本目录内容说明

- `source_job.log`
  - 原始 Harbor 作业总日志
- `source_result.json`
  - 原始 Harbor 作业聚合结果
- `passed_13`
  - 13 个通过题的 trial 目录
- `finished_reward0_26`
  - 26 个正常结束但未通过题的 trial 目录
- `agent_timeout_4`
  - 4 个 `AgentTimeoutError` 题目的 trial 目录

