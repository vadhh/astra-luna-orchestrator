# DeepSeek Flash baseline vs GPT-5.6 Luna estimate

This is a price-model comparison using the upstream repository's September 19,
2026 thin-orchestration measurement. It is not a new model-performance
benchmark: no new inference was run, and the upstream report does not publish
the worker's cached-input or output-token totals.

## Shared workload record

| Metric | Upstream thin orchestration |
| --- | ---: |
| Implementation and test lines | 47,949+ |
| Astra input tokens | 4,600,297 |
| Worker input tokens | 1,001,537,994 |
| Reported total compute per 1K lines | $0.26–$0.34 |

## Published worker rates per 1M tokens

| Token type | DeepSeek V4.1 Flash baseline | GPT-5.6 Luna | Luna / Flash |
| --- | ---: | ---: | ---: |
| Uncached input | $0.15–$0.30 | $0.20 | 0.67–1.33× |
| Cached input | $0.003–$0.006 | $0.02 | 3.33–6.67× |
| Output | $0.60–$1.20 | $1.20 | 1–2× |

The Flash figures are copied from the upstream benchmark. Luna's current rates
are from the [official model page](https://developers.openai.com/api/docs/models/gpt-5.6-luna).

## What the upstream data supports

If every recorded worker input token were uncached, Luna's worker-input cost
would be $200.31 ($4.18 per 1K lines). If all were cached, it would be $20.03
($0.42 per 1K lines). The latter is still incomplete because output and cache
writes are not reported.

Using the upstream Astra cached-input estimator ($1/M) and assuming zero output
cost gives a best-case combined floor of $24.63, or **$0.51 per 1K lines**. The
actual Luna total will be higher. It therefore cannot be compared directly with
the upstream $0.26–$0.34 figure without the original token-type breakdown.

## Conclusion

Luna removes the external DeepSeek/Router dependency, but its published cached
input rate is materially higher. Use this fork for native-Codex simplicity; do
not claim the upstream DeepSeek cost saving for Astra–Luna until a matched live
evaluation records cached input, output, latency, and task success.
