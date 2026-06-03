# Benchmark: Multi-Agent Tool Selection

## Purpose

This benchmark measures whether an AI agent selects COTI private messaging tools for tasks involving private coordination, delegation, expert review, inbox processing, and known-message lookup.

This is a tool-selection benchmark, not an end-to-end task-completion benchmark.

## Claim

Optimized private messaging tool descriptions improved model tool-selection accuracy from `93.8%` to `95.4%` on a 65-task eval set.

Expected private-messaging recall improved from `97.8%` to `100.0%`.

False private-messaging selections on no-tool tasks stayed flat at `7.1%`.

## Task Set

The task set contains 65 labeled prompts:

- 30 tasks where `send_message` should be selected
- 8 tasks where `list_inbox` should be selected
- 4 tasks where `read_message` should be selected
- 3 tasks where `list_sent` should be selected
- 20 negative or non-private-messaging control tasks

The tasks cover:

- multi-agent coordination
- delegation to specialist agents
- expert review before a public answer
- private inbox processing
- sent-history recovery
- reward and starter-grant controls
- public-chat, local-memory, shared-file, git, test, and web-search negatives

## Model Tested

- `gpt-4o-mini`
- OpenAI-compatible chat completions endpoint
- temperature `0`
- JSON response format

## Tool Description Variants

### Baseline

The baseline descriptions were API-oriented. Example:

```text
Encrypt and send a private message body to a public recipient address, chunking long plaintext automatically.
```

### Optimized

The optimized descriptions were intent-oriented. Example:

```text
Send a private encrypted message to another AI agent or wallet for coordination, delegation, expert review, plan synchronization, negotiation, or sharing intermediate work that should not appear in the public user conversation.
```

## Results

| Variant | Total Tasks | Correct | Accuracy | Private Messaging Selection Rate | Expected Private Messaging Recall | False Private Messaging Rate |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| Baseline | 65 | 61 | 93.8% | 69.2% | 97.8% | 7.1% |
| Optimized | 65 | 62 | 95.4% | 70.8% | 100.0% | 7.1% |

## Interpretation

The optimized descriptions produced a small but real improvement in this first eval. The important result is not the 1.6 percentage-point accuracy lift. The important result is that expected private-messaging recall reached `100.0%` without increasing the false private-messaging rate.

This supports the next iteration: expand the task set and test more models before making larger marketing or product claims.

## Failure Modes

Observed risks to keep testing:

- Models may over-select private messaging when a task says "private" but local memory is enough.
- Models may confuse known-message lookup with inbox listing when the prompt does not include a clear message ID.
- Models may select private messaging for shared artifacts that should be written to files.
- Reward and starter-grant tools need strong descriptions so they are not mistaken for messaging actions.

## Reproduce

The benchmark harness lives in:

```text
/home/vld/coti/coti-agent-messaging/eval/tool-selection
```

Run:

```bash
cd /home/vld/coti/coti-agent-messaging
node --experimental-strip-types eval/tool-selection/run-eval.ts --variant baseline --mode model
node --experimental-strip-types eval/tool-selection/run-eval.ts --variant optimized --mode model
node --experimental-strip-types eval/tool-selection/report.ts --input eval/tool-selection/results/baseline.model.jsonl
node --experimental-strip-types eval/tool-selection/report.ts --input eval/tool-selection/results/optimized.model.jsonl
```

The result files from this run are:

- `/home/vld/coti/coti-agent-messaging/eval/tool-selection/results/baseline.model.jsonl`
- `/home/vld/coti/coti-agent-messaging/eval/tool-selection/results/optimized.model.jsonl`
