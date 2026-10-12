# P2: Automated LQA Pipeline

An LLM judge does first-pass quality review on machine translations. Anything critical or low-confidence is routed to a human reviewer.

**Demo (2 min):** (https://www.loom.com/share/8907df606caf4c80bbcf7734bb50d733)

## How it works

1. **Read segments:** pulls source and target from a Google Sheet
2. **LLM Judge:** Claude Sonnet 5.5 (Anthropic API, temperature 0) scores each segment with an MQM-style typology: error count, highest severity, categories, summary, confidence
3. **Write to Output:** every result is saved
4. **If critical or low confidence → Send to Human Review**

Built in n8n by a localization professional with no engineering background.

## Test on 10 new segments

10 FLORES+ segments (en → ru-RU, translated by DeepL) not used to build or tune the prompt. I reviewed them first, timed, before running the pipeline.

| Measure | Result |
| --- | --- |
| Manual review | 61 s per segment |
| Pipeline | 1.4 s per segment |
| API cost | $0.005 per segment (11,170 tokens in, 2,921 out for 10) |
| Severity matches with my review | 3 of 10 |
| Major errors the judge missed | 2 |

**Takeaway:** speed and cost aren't the bottleneck; severity calibration is. The judge finds issues but rates them lower than a human reviewer would. An explicit rule ("incomplete translation = Critical") fixed one case. Next round tests a full severity guide on another fresh set.

## Limitations

- 10 segments, one language pair, one reviewer
- FLORES+ is public, so the model may have seen these sentences in training
- Severity rules are a judgment call without a project style guide

## Files

- `lqa-pipeline.json`: the n8n workflow (import into n8n to reuse; no API keys included)
- `p2_input_fresh10.csv`: the 10 test segments
- `p2_manual_review.csv`: my timed manual review
- `p2_judge_output.csv`: the judge's scores for every segment (rows 12-21)
- `p2_human_review.csv`: segments routed to a human (0 for this experiment)
- `p2_comparison.csv`: my severity against the judge's, plus time and cost summary

Data: FLORES+ (devtest), CC BY-SA 4.0. Model and run date: Claude Sonnet 5.5, October 8, 2026.
