# Project Plan Blueprint — HOLY Marketing Analyst Case

*A map of how the case was approached, what was produced, and where to find everything. Read this first.*

---

## 1. Deliverables map

| File | What it contains |
|---|---|
| **[project-plan-blueprint.md](project-plan-blueprint.md)** | This document — the plan, method, and decision log |
| **[data-quality-review.md](data-quality-review.md)** | Full audit of the 4 CSVs: verified errors, anomalies, cleaning rules applied, and the top 10 questions for the interviewer |
| **[q1-campaign-analysis.md](q1-campaign-analysis.md)** | Question 1 — Syrup Campaign analysis, written as a briefing for the Head of Marketing (KPIs, uplift, adoption, delays, retention, cannibalisation, recommendations) |
| **[q2-h2-budget-plan.md](q2-h2-budget-plan.md)** | Question 2 — €35M H2 budget allocation, incrementality testing roadmap, and strategic reflection on the MMM memo + attribution data |
| **[charts/](charts/)** | Four PNG charts referenced by the Q1 write-up |
| **[analysis.ipynb](analysis.ipynb)** | The executable analysis: data audit (§1), cleaning rules (§2), every Q1 computation (§3), chart generation (§4), Q2 memo extraction & budget arithmetic (§5) — the .md files link into its sections |

---

## 2. Workflow (in execution order)

### Phase 0 — Intake
1. Read `case-definition.md` (both questions, schemas, constraints, guiding questions).
2. Inventoried the project folder; extracted the CMO memo PDF to text (3 pages: MMM v5.3.1 results, H2 recommendation, DE attribution Mar–May 2026).
3. Previewed all four CSVs — **immediately found the schemas don't match the brief** (no `channel`/`is_cancelled` columns, extra columns, different file/field names), which promoted the data audit from a formality to a first-class workstream.

### Phase 1 — Data quality audit (before any analysis)
Scripted checks in Python/pandas across all files: uniqueness, referential integrity, nulls, value ranges, cross-file reconciliation (order revenue vs. sum of items; shipments vs. orders), timestamp sanity, and campaign-specific validations (do the brief's SKUs exist? do 3er bundles really start May 12?).
**Output:** `data-quality-review.md`. The audit also produced the **cleaning rules** used downstream (dedupe, orphan removal, cancelled-order definition, "use gross revenue", "compute delivery delay manually").

Key catches that shaped the analysis:
- `net_revenue` is unreliable (definition flips for ~11% of orders; 5.5K negative item rows) → all analysis uses **gross**.
- `shipments` has re-shipment duplicates and a broken `is_on_time` → delay computed as first `delivered_at − order_date`.
- Two post-campaign business events (May 18 sampling giveaway, May 27 Summer Cocktail launch) contaminate the post-period → called out wherever post-period numbers are used.

### Phase 2 — Question 1 analysis
Design decisions, then scripted analysis:

| Decision | Choice | Why |
|---|---|---|
| Comparison windows | Pre Apr 1–18 · Campaign Apr 19–May 4 · Post May 5–31 | Only pre-period available as baseline (first-ever product; dataset starts Apr 1) |
| Baseline metric | Per-day averages (windows have unequal lengths) | Comparability |
| Money metric | Gross revenue, ratios/relative deltas | Net unreliable; brief says don't trust absolutes |
| Syrup scope | Bottles `10-00-42-0001-*`, 10er pods `11-00-43-0001`, 3er `11-00-43-0002%` as reorders (per brief) | Brief definition; mystery SKU `10-00-42-0020` flagged but excluded pending clarification |
| Retention cohort | Campaign bottle buyers with a `customer_id`, first bottle order per customer, any later syrup purchase counts | Cleanest observable definition given identity gaps |
| Delay impact | Reorder rate by delivery-delay bucket of the first bottle order | Direct read of the production-gap question; time-truncation caveat stated |
| Cannibalisation | Energy €/day pre vs post + bottle-buyer cohort vs non-bottle existing-customer control | Cohort contrast controls for overall market growth |

Analysis blocks executed: baseline & uplift (total, per market, excl. launch day) → new-vs-existing mix → market breakdown → daily trends → category mix → adoption (penetration, flavours, bundle structure, attach rates) → buying behaviour (AOV, units) → cross-purchase → delivery-delay impact → retention/reorders → cannibalisation → post-campaign event forensics (May 12/18/27 spikes).

### Phase 3 — Charts
Four charts, built per the dataviz method (form chosen by the data's job, single-hue palette since every chart encodes one measure, direct labels, annotated events): daily revenue with campaign window shaded · daily bottle units (full + zoomed small-multiple, because the launch spike is 20× the baseline) · market adoption bars · **delay-vs-reorder bars (the money chart)**.

### Phase 4 — Question 2 (desk analysis of the memo)
1. Extracted the memo PDF with `pypdf` and transcribed every table into DataFrames (L1/L2 iROAS & mROI, H1 run-rates, constraints, attribution) — notebook §5.1–5.2.
2. Identified the central tension: the task's **€35M** vs the MMM's own optimal **€16.1M** — made this the opening argument rather than hiding it.
3. Built the allocation from mROI ranking + saturation caveats + contract floors, with a testing reserve and Q3/Q4 phasing.
4. Designed the testing roadmap around the three biggest MMM-vs-attribution conflicts (Influencer, Google/Affiliate, TikTok/META), with sequencing and a Black-Week test freeze.
5. Wrote the strategic reflection: assumptions with confidence levels, 9 flagged data oddities and how each was handled, 10 missing-context asks.

### Phase 5 — Write-ups
Each deliverable written for its audience: Q1 as a marketing-leadership briefing (findings first, method in footnotes), Q2 as a planning document (logic per line item), data review as an engineering-style audit (issue → evidence → handling → question).

---

## 3. Principles followed

- **Audit before analysis** — every headline number sits on the cleaning rules from Phase 1, and those rules are documented and reversible.
- **Data vs. interpretation kept separate** — write-ups label "what the data shows" vs. "my hypothesis" (per the case's explicit requirement).
- **Relative numbers over absolutes** — per the dataset disclaimer, all conclusions are ratios, shares and deltas.
- **Reproducibility** — everything numeric comes from [analysis.ipynb](analysis.ipynb) (no spreadsheet hand-edits); the write-ups cite notebook sections (`code §…`) so any figure can be regenerated or challenged.
- **Say the uncomfortable thing** — the Q2 answer leads with the budget being ~2× model-optimal; the Q1 answer quantifies what the production gap cost in retention.

## 4. Known limitations of this work

- No media spend data for Q1 → uplift measured, ROI not measurable.
- Retention window is only 4–6 weeks post-purchase; 90-day retention and pods consumption cycles need June+ data.
- Post-campaign baseline is contaminated by two unrelated events (documented, not fully removable).
- Q2 assumes the MMM's response curves extrapolate to ~2× spend — explicitly flagged as low-confidence.
- Open data questions (see the data quality review) could shift details, though not the direction, of the findings.

## 5. Rough time log

| Phase | Time |
|---|---|
| Intake + PDF/data exploration | ~30 min |
| Data quality audit | ~45 min |
| Q1 analysis + charts | ~1.5 h |
| Q2 desk analysis | ~45 min |
| Write-ups | ~1.5 h |
| **Total** | **~5 h** (tool-assisted, fully scripted) |
