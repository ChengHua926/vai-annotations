# VAI annotations: policy-compliance labels for tau3-bench and ClawsBench runs

Task-level policy-compliance labels for 387 agent runs, released with the paper "Holding Agents Accountable with Verifiable Agent Insurance" (under review): 327 tau3-bench runs (airline and retail; GLM-4.7-Flash 164, Qwen3-30B-A3B 163) and 60 ClawsBench Standard60 runs (GLM-5.2 in OpenClaw), all recorded with the updated harnesses. Each run carries the trace the labelers saw, the independent votes of three LLM labelers, and the final label used in the paper.

The labels for the earlier runs of the first preprint are at the tag `arxiv-v1`.

## Layout

```
tau/
  traces/rr-001..rr-327.json            the run as presented to the labelers: policy sentences, user scenario, numbered steps
  index.json                            run id -> model, domain, benchmark task, simulation id
  votes/{claude-fable-5, gpt-5.6-sol,   one row per run: verdict, cited rules and steps, the full reply, reasoning text where the provider returns it
    kimi-k3}.jsonl
  labels.jsonl                          final label per run, the three votes, and the rules at least two labelers cited
  policies/airline.md, retail.md        the tau3-bench policies the runs were judged against
clawsbench/
  traces/claws-rr-01..claws-rr-60.json  same, one file per Standard60 task
  index.json                            run id -> Standard60 task
  votes/…, labels.jsonl                 as above
  policies/assistant_v1.md              the ClawsBench policy given to the agent
prompts/tau_v1.md, clawsbench_v2.md,    the frozen labeling prompts
  SHA256SUMS
selection/tau.json, clawsbench.json     how runs were selected and named
```

## How the labels were made

Three models (Claude Fable 5, GPT-5.6 Sol, Kimi K3) labeled every run independently with the frozen prompts, reporting a verdict (`violation` or `no_violation`) and, for violations, the policy sentence and trace steps that support it. The final label for a run is the verdict at least two of the three models gave. `labels.jsonl` gives that label, each model's vote, and the rules at least two models cited.

Votes are the last successful row per labeler and run. On three ClawsBench runs (`claws-rr-30`, `claws-rr-34`, `claws-rr-54`) Claude Fable 5 refused the request (the provider's content filter), recorded with `status: refused`. On `claws-rr-34` the two remaining votes disagree, so the run has no label (`"label": null`, `"unresolved": "tie"`) and is left out of the paper's counts.

Run ids are the labeling ids, assigned in shuffled order; the index files map them to the benchmark tasks. One tau3-bench task has no Qwen3-30B-A3B run because both attempts ended in an infrastructure error (`selection/tau.json`, `missing`). Local paths inside the traces were rewritten to relative ones.

## Licenses

Our labels, votes, prompts, and index files: CC BY 4.0 (`LICENSE`). The traces contain benchmark material: tau3-bench policies and task data (Sierra Research, MIT License) and ClawsBench tasks, policy, and mock-service state (BenchFlow env0, GNU AGPL v3.0). See `NOTICE.md`.
