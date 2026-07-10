# HOLY Marketing Analyst Case — Solution

*Rodrigo Uehara Guskuma · July 2026*

Solution to the two-part HOLY case study: **Q1 — 5th BURRsday Syrup Campaign launch analysis** (Apr 19 – May 4, 2026, DE/FR/UK) and **Q2 — H2 2026 budget allocation, incrementality-testing roadmap and strategic reflection** (based on the MMM v5.3.1 memo + DE attribution data).

## 📄 Start here

**[holy-case-solution.md](holy-case-solution.md)** — the full write-up: Q1 briefing for the Head of Marketing, Q2 budget plan, plus appendices (seven further analyses, method blueprint, data quality review). Also available as [holy-case-solution.pdf](holy-case-solution.pdf).

## Repository map

| Path | What it contains |
|---|---|
| [holy-case-solution.md](holy-case-solution.md) | **The complete solution** (Q1 + Q2 + appendices) — single-document version |
| [analysis.ipynb](analysis.ipynb) | The executable analysis: data audit (§1), cleaning rules (§2), every Q1 computation (§3), charts (§4), Q2 memo extraction & budget arithmetic (§5), further analyses (§6). Every number in the write-ups carries a `(code: analysis.ipynb §…)` tag pointing to the section that computes it |
| [docs/project-plan-blueprint.md](docs/project-plan-blueprint.md) | How the case was approached: workflow, design decisions with rationale, principles, limitations, time log |
| [docs/data-quality-review.md](docs/data-quality-review.md) | Full audit of the four CSVs: verified issues, cleaning rules, open questions — plus the additional definitions provided by Martijn and how the analysis was re-run under them |
| [docs/q1-campaign-analysis.md](docs/q1-campaign-analysis.md) | Q1 as a standalone document |
| [docs/further-analysis.md](docs/further-analysis.md) | Seven analyses beyond the brief — including the truncation-corrected delay estimate that revised a headline number down |
| [docs/q2-h2-budget-plan.md](docs/q2-h2-budget-plan.md) | Q2 as a standalone document |
| [charts/](charts/) | The four PNG charts referenced by the Q1 write-up |
| [case-materials/](case-materials/) | The original brief and CMO memo (inputs, unchanged) |
| [case-study-dataset/](case-study-dataset/) | The four CSVs as delivered (orders, order items, products, shipments) |

## TL;DR of the findings

**Q1** — The launch worked as a demand event (+118% net revenue/day vs. pre-period; 45% of campaign orders contained a bottle) but was an existing-customer launch, not an acquisition one (22.7% of bottle orders from first-timers vs. 47.8% elsewhere). DE carried it; FR underperformed its size. The production gap was the expensive lesson: late-campaign buyers waited a median 38–40 days, costing ≈1,000 early reorders on a truncation-corrected estimate. The headline positive: 10.1% of bottle buyers re-bought syrup within the observed window — at core-category level despite the delivery crisis — so the refill model works.

**Q2** — The task's €35M is 2.2× the MMM's own optimal plan, and the write-up opens with that tension rather than hiding it. The allocation ranks channels by marginal ROI, phases ~40/60 toward Q4, restructures Influencer's mix rather than just cutting it, and reserves €2.5M for the incrementality tests that target exactly where MMM and attribution contradict each other most.

## Reproducibility

- Everything numeric is computed in [analysis.ipynb](analysis.ipynb) (Python/pandas) — no spreadsheet hand-edits. The notebook is committed with outputs, so it can be read on GitHub without running anything.
- Data prep applied throughout (details in the [data quality review](docs/data-quality-review.md)): duplicate orders deduped · orphan line items dropped · cancelled orders excluded per the confirmed rule `refunded_value ≥ gross_revenue` · `10-00-42-002x` spare-part SKUs excluded · **net revenue** as the money metric (per the confirmed definition: item gross + shipping − refunds − tax) · delivery delay computed as `delivered_at − order_date`.
- Per the dataset's own disclaimer, absolute numbers are anonymised — conclusions are stated as ratios, shares and relative comparisons.

## Time spent

~7 hours end-to-end (tool-assisted, fully scripted): ~5h on Q1 incl. audit, charts and further analyses, ~1.5h on Q2, plus the re-run under Martijn's confirmed definitions and an adversarial self-review. Full time log in the [project blueprint](docs/project-plan-blueprint.md).
