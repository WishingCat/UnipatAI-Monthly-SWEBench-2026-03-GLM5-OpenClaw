# UnipatAI Monthly SWEBench 2026-03 GLM5 OpenClaw

This repository stores selected Harbor run artifacts instead of putting all records in the repository root.

## Repository Layout

- `Run1-43/`
  - First archived batch of Harbor trial records
  - Contains 43 selected task records copied from a 63-trial landed run
- `Run1-43/passed_13/`
  - 13 tasks with `reward = 1.0`
- `Run1-43/finished_reward0_26/`
  - 26 tasks that finished normally but did not pass the verifier
- `Run1-43/agent_timeout_4/`
  - 4 tasks that ended with `AgentTimeoutError`
- `Run1-43/source_job.log`
  - Original Harbor job-level log
- `Run1-43/source_result.json`
  - Original Harbor aggregated result file
- `Run1-43/README.md`
  - Batch-specific notes for this run

## Organization Rule

Future uploads should be added as new top-level batch folders, rather than placing task records directly in the repository root.
