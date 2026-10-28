# My Take on Jev

## In plain English

This project tests several models on the same support-routing decisions and checks two things: whether their answers are correct and whether their confidence is honest. The shared test makes the comparison easier to verify.

**Status:** Completed independent evaluation using a made-up support dataset.


**Its open rivals, measured.** An independent test of open System One decision models on support routing, and of whether their confidence is honest.

Decision models answer typed questions (yes or no, pick one, rate 1 to 5) with a probability for every answer, instead of writing text. Within weeks of TypeSafe's Jev, several open ones appeared, measured almost only by their makers. This project tests them, alongside a general language model and plain keyword rules, on 250 made-up support requests, and asks two things: are they accurate, and **when they say 90% sure, are they right 90% of the time?**

**Read the report:** [technical report](https://ansarbuilds.dev/p/decision-models/report.html) · [PDF](https://ansarbuilds.dev/p/decision-models/report.pdf) · [source](paper/report.md) · [citation](CITATION.cff)

## Results

50 held-out requests, scored once, after every prompt, rule and threshold was fixed. Models ran on a MacBook Air M4 (16 GB); the rules ran in a cloud container.

| Contender | Team right | Urgent right | Calibration error (urgent / team / frustration; 0 is perfect) | Seconds per request |
|---|---|---|---|---|
| Strands Decider 2B (decision model) | **80%** | 82% | **0.05 / 0.17 / 0.06** | 1.45 |
| Keyword rules | 78% | 84% | 0.07 / 0.44 / 0.37 | 0.0001 |
| qwen2.5:7b (general model) | 64% | 82% | 0.13 / 0.22 / 0.43 | 7.72 |
| Kev-0.8B (decision model) | 60% | 80% | 0.16 / 0.12 / 0.50 | 0.34 |
| Laya-typed-decisions (decision model) | 48% | 88% | 0.25 / 0.29 / 0.18 | 0.32 |

- A 2B decision model beat a 7B general model by 16 points on routing, five times faster, with the most honest confidence.
- The general model was confidently wrong: near-certain on 89 development answers, right on 68% of them.
- How a general model is asked matters: a probability-per-option prompt piled 174 of 200 answers onto the first three options listed.
- The keyword rules' near-tie is a warning about shared vocabulary between test set and rules, not a win. See the report's limits.

## What's here

| Path | What |
|---|---|
| `paper/report.md` | The full technical report |
| `CASE_STUDY.md` | The short version |
| `NOTES.md` | Dated log of every run, including the failures |
| `docs/labelling-rules.md` | Labelling rules, written before any request |
| `docs/models.md` | Every model checked, with licences |
| `data/` | The 250 requests (`dev.jsonl`, `heldout.jsonl`, `source/`) |
| `systemone/` | One interface for every contender, with answer checking |
| `scripts/` | Build the test set, run, score, hard cases, fast-then-slow, report |
| `results/` | Scored results, calibration charts and every raw answer |

## Reproduce

Python 3, standard library only for the harness:

```
python3 scripts/build_testset.py     # rebuild and check the test set
python3 -m unittest                  # 59 tests
python3 scripts/run.py rules         # any contender on the development set
python3 scripts/score.py             # tables and calibration chart
```

Each decision model runs behind its own local `/v1/systemone` server; the commands are in `docs/models.md` and `NOTES.md`.

## About

Built by [Mohamed Ansar](https://ansarbuilds.dev) as a personal project. Made-up data only; nothing here comes from any employer. See `LICENSE`.
