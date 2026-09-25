# VAI annotations: policy-compliance labels for tau3-bench and ClawsBench runs

Task-level policy-compliance labels for 388 agent runs, released with the paper "Holding Agents Accountable with Verifiable Agent Insurance" (under review): 328 tau3-bench runs (airline and retail; GLM-4.7-Flash and Qwen3-30B-A3B, 164 each) and 60 ClawsBench Standard60 runs (GLM-5.2 in OpenClaw). Each run carries the trace the labelers saw, the independent votes of three LLM labelers, and the final label used in the paper.

## Layout

```
tau/
  traces/run-01..run-30.json, ext-001..ext-298.json   the run as presented to the labelers: policy sentences, user scenario, numbered steps
  index.json                                           run id -> model, domain, benchmark task, calibration or extension set
  votes/{claude-fable-5, gpt-5.6-sol}.jsonl,          one row per run: verdict, cited rules and steps, the full reply, reasoning text where the provider returns it
    kimi-k3-1.jsonl, kimi-k3-2.jsonl                  (Kimi K3 split in two files; runs in the same order)
  labels-1.jsonl, labels-2.jsonl                       final label per run, cited rules, and for extension runs the vote summary (split in two files)
  policies/airline.md, retail.md                       the tau3-bench policies the runs were judged against
clawsbench/
  traces/<task>.json                                   same, one file per Standard60 task
  index.json                                           calibration and extension task lists
  votes/…, labels.jsonl                                as above
  policies/assistant_v1.md                             the ClawsBench policy given to the agent
prompts/tau_v1.md, clawsbench_v1.md, SHA256SUMS        the frozen labeling prompts
selection/calibration.json, extension.json             how runs were selected and named
```

## How the labels were made

For each benchmark, 30 runs form a calibration set and the rest an extension set. Three models (Claude Fable 5, GPT-5.6 Sol, Kimi K3) labeled every run independently with the frozen prompts, reporting a verdict (`violation` or `no_violation`) and, for violations, the policy sentence and trace steps that support it. The final label for a run is the weighted committee decision; the weights are per-labeler Dawid–Skene error rates fitted on the calibration set, as described in the paper. `labels.jsonl` gives that label, the union of rules cited by the labelers voting `violation`, and for extension runs the three votes and the posterior.

Votes are the last successful row per labeler and run. One ClawsBench task, `auth-service-account-impersonation`, has two votes: Claude Fable 5 refused the request (the provider's content filter), recorded with `status: refused`.

Run ids are the labeling ids (`run-NN` for the calibration set, `ext-NNN` for the extension set); `tau/index.json` maps them to the benchmark task. `quality_group` in `labels.jsonl` marks 11 ClawsBench runs whose agent timeline was incomplete in the labeling view; they keep their labels.

## Licenses

Our labels, votes, prompts, and index files: CC BY 4.0 (`LICENSE`). The traces contain benchmark material: tau3-bench policies and task data (Sierra Research, MIT License) and ClawsBench tasks, policy, and mock-service state (BenchFlow env0, GNU AGPL v3.0). See `NOTICE.md`.
