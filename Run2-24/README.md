# Run2-24

这个目录保存的是 `monthly_swebench_openclaw_non_alpine_2026-04-10_10-55-30` 这批运行结果中，筛选出的 24 个任务样本：

- `PASS`：6 个，正常完成，`reward = 1.0`
- `COMPLETE_FAIL`：15 个，正常完成并发生多轮 episode，但 `reward = 0.0`
- `RAN_FAIL`：3 个，已经进入多轮 episode，但最终因超时或运行期异常结束

## 顶层结构

`Run2-24/` 下面按用途分成 6 部分：

- `tasks/`：24 个任务的逐题结果目录，每个任务一个子目录
- `job_meta/`：原始 Harbor job 级别的汇总文件、状态文件和批次报告
- `logs/`：这批 job 对应的 stdout、stderr、supervisor 日志
- `selection_summary.json`：本次筛选出的 24 个任务清单，字段包含 `task`、`status`、`result`、`episode_count`、`trial_name`
- `selection_summary.csv`：与 `selection_summary.json` 对应的表格版清单，便于直接查看和导入
- `copy_summary.json`：本次复制整理动作的摘要，记录目标目录、任务数量、文件数量、总体积和最大文件信息

## 单题目录结构

`tasks/<task_id>/` 目录下通常包含两部分：

- `task_summary.json`
  - 当前任务的单题摘要
  - 字段包含 `task`、`status`、`result`、`episode_count`、`trial_name`
  - 同时保留原始来源路径：`source_job_dir`、`source_trial_dir`
- `trial/`
  - 当前任务对应的完整 trial 运行产物

`trial/` 里面的常见内容如下：

- `config.json`：该 trial 的运行配置
- `result.json`：该 trial 的结果文件
- `trial.log`：trial 级日志
- `agent/`：OpenClaw/agent 运行过程文件，常见文件包括 `openclaw-output.txt`、`openclaw-session.jsonl`、`openclaw-sessions/`、`trajectory.json`、`setup/`、`command-*`
- `verifier/`：verifier 输出，常见文件包括 `reward.txt`、`test-stdout.txt` 等
- `artifacts/`：运行过程中额外生成的工件目录

## 关于 episode 数据

仓库中的 `Run2-24` 已按上传要求移除了所有 `episode_exports/` 目录，因此：

- 不包含逐 episode 导出的 `0001_episode-*.json`
- 不包含 `episode_exports/summary.json`
- 不包含 `episode_exports/episodes.jsonl`

这样做是为了减小仓库体积并避免上传大量对话导出文件。  
如果需要完整的 episode 导出，需要回到本地工作目录中的原始备份版本查看。

## 推荐阅读顺序

如果想快速理解某个任务，建议按下面顺序查看：

1. `selection_summary.csv` 或 `selection_summary.json`
2. `tasks/<task_id>/task_summary.json`
3. `tasks/<task_id>/trial/result.json`
4. `tasks/<task_id>/trial/verifier/`
5. `tasks/<task_id>/trial/agent/`
6. `job_meta/result.json` 和 `logs/`
