# Q2 — H2 2026 Sales Budget: Allocation, Testing Roadmap & Strategic Reflection

*Inputs: MMM v5.3.1 memo (Section A) + DE attribution data Mar–May 2026 (Section B). Constraints: €35M total · €4M brand (fixed) · €800K TV locked in Q3 · €31M flexible performance budget.*

> **Code:** the memo PDF extraction, its tables transcribed as data, and every derived number in this document are in **[analysis.ipynb](analysis.ipynb) §5** — PDF extraction [§5.1](analysis.ipynb#sec51), memo tables [§5.2](analysis.ipynb#sec52), derived analysis & MMM-vs-attribution conflicts [§5.3](analysis.ipynb#sec53), allocation checksums [§5.4](analysis.ipynb#sec54).

---

## 0. The elephant in the room (read this first) *(code [§5.3](analysis.ipynb#sec53))*

The MMM team's own optimised plan spends **€16.1M** in H2 — the task asks me to allocate **€35M**, i.e. **2.2× the model-optimal level** and ~1.8× the H1 run-rate (~€19.3M/half). The MMM says META's marginal ROI is already ~0.99 at *current* spend and TikTok's drops to 0.63 at just +50% spend. **At €31M of performance spend, the marginal euros are, per the model, value-destructive.**

I therefore treat the €35M as a hard constraint (growth/share-taking mandate, investor commitment, etc.) and build the plan that **minimises marginal-ROI damage, weights spend toward Q4** (Black Week/Christmas, when response curves support more spend), and **converts part of the budget into measurement** so the next planning cycle isn't flying blind. But my first recommendation as an analyst is explicit: *if the €35M is negotiable, right-size media to ~€20–24M and put the remainder into retention/CRM, promo depth, or reserves.*

---

## Task 1 — H2 Budget Allocation

### 1.1 Allocation logic

1. **Rank by marginal ROI (mROI), not average iROAS** — iROAS rewards history; mROI prices the next euro. Ranking: Google 4.46 > Affiliate 1.78 > Paid Social 1.50 (TikTok 1.35 / META 0.99) > Brand 0.44 ≈ Influencer 0.40.
2. **Scale the small high-mROI channels first** (Google, Affiliate) — but capped, because the memo itself flags their numbers as attribution-assisted; scaling is gated by incrementality tests.
3. **Paid Social takes the largest share**, tilted to TikTok early and META for depth — accepting that both saturate below mROI 1 at this budget.
4. **Influencer is restructured, not just cut.** The MMM says the *mix* is the problem (YouTube Inf. 0.30 vs Podcast 0.71 / TikTok Inf. 0.76). Because €31M must be deployed, Influencer keeps a substantial envelope, shifted toward the high-mROI platforms as far as contract floors allow.
5. **Hold a testing & flex reserve** — with model holdout R² of 0.29, buying information is worth more than the last saturated media euro. The reserve deploys in Q4 into whatever the Q3 tests validate.
6. **Phase Q3 < Q4** (≈ 40/60): H2 contains Black Week + Christmas; saturation ceilings are higher when demand is elevated.

### 1.2 Channel-by-channel plan (H2 totals) *(code [§5.4](analysis.ipynb#sec54))*

| Channel | H1 actual (≈26wk) | **H2 plan** | vs H1 | Logic |
|---|---|---|---|---|
| **Paid Social** | €5.9M | **€17.0M** | +186% | Largest share; least-bad saturation profile. META €10.0M (workhorse, mROI ~1 → run to breakeven), TikTok €5.5M (momentum, front-scale but gated by the Aug stress test), YouTube paid €1.5M (scaling test of the "37× iROAS" signal — treated as unproven, capped, staged) |
| **Influencer** | €7.0M | **€9.6M** | +37% | Forced above optimal by the budget constraint. Mix shift: YouTube Inf. share cut from ~50% toward Podcast / TikTok Inf. / Instagram (mROI 0.5–0.8); renegotiate or exit floor-driven contracts; whitelisting/paid-amplification bridges Influencer → Paid Social |
| **Google Search** | €0.15M | **€1.0M** | ~6.7× | Highest mROI, tiny base. Phase: 2× run-rate in Q3 (the memo's controlled test), scale to plan in Q4 only if the brand-keyword holdout confirms incrementality |
| **Affiliate** | €0.46M | **€0.9M** | ~2× | mROI 1.78; scale moderately; audit coupon-poaching first |
| **Testing & flex reserve** | — | **€2.5M** | — | Funds incrementality tests + Q4 reallocation into validated winners (likely destinations: TikTok scale-up, Google, or promo support in Black Week) |
| **Performance subtotal** | €13.5M | **€31.0M** | | |
| **Brand (fixed)** | €5.8M | **€4.0M** | −31% | See split below |
| **TOTAL** | ~€19.3M | **€35.0M** | +81% | |

### 1.3 Brand envelope (€4.0M) *(code [§5.4](analysis.ipynb#sec54))*

Allocated by the L2 iROAS ranking, with the memo's caveat that the 8-week window *understates* brand (so I don't zero-out anything structural):

| Sub-channel | iROAS (8wk) | H2 plan | Logic |
|---|---|---|---|
| TV — Q3 (locked) | 1.12 | **€0.80M** | Contractual |
| Events / Sponsoring | **1.74** | **€1.50M** | Best measured brand ROI; scale |
| TV — Q4 top-up | 1.12 | €0.60M | Above water even on the conservative window; supports Q4 trading |
| OOH | 0.97 | €0.40M | ~Breakeven on a lower-bound measure → hold, don't grow |
| Streaming | 0.92 | €0.25M | Same logic, smaller |
| Influencer Awareness | 0.69 | €0.30M | Cut hard; overlaps with performance influencer |
| Performance Awareness | 0.43 | €0.15M | Near-exit; keep a measurement stub |
| **Total** | | **€4.00M** | |

### 1.4 Quarterly phasing *(code [§5.4](analysis.ipynb#sec54))*

| | Q3 | Q4 | Rationale |
|---|---|---|---|
| Performance | €12.0M | €19.0M | Black Week + Christmas raise saturation ceilings; Q3 runs the tests that de-risk Q4 |
| Brand | €2.2M | €1.8M | TV lock + events season in Q3; Q4 brand supports peak trading |
| **Total** | **€14.2M** | **€20.8M** | |

---

## Task 2 — Testing Roadmap

**Principle:** every €1M+ allocation decision that rests on a disputed number gets an incrementality test *before* the Q4 reallocation gate (end of September). The tests target exactly the three places where MMM and attribution disagree most.

| # | Test (priority order) | Hypothesis | Method | Timing | Decision trigger |
|---|---|---|---|---|---|
| 1 | **Influencer incrementality** — the biggest money on the most disputed number (MMM mROI 0.40 vs attribution CLV6M/CAC ≈ 100%) | ≥50% of attributed influencer NCs would have converted anyway | Creator-level holdout: pause a matched ~30% panel of DE creators for 6 weeks; measure regional/audience NC delta vs control panel (geo-split is impractical for national creators) | **Jul–mid Aug** | Incremental CAC > €85 (≈1.5× attributed) → cut Influencer to floor in Q4, reserve moves to PS/Google. Incremental CAC ≤ CLV6M → restore mix-shifted budget |
| 2 | **Google Search brand-keyword holdout** — cheap, fast, independent | ≥60% of brand-search conversions are non-incremental | Pause brand keywords in a matched set of DE regions (geo split) for 4 weeks | **Jul** (parallel to #1 — different mechanism, minimal interference) | True incrementality <40% → cap Google at 2× run-rate, iROAS 4.09 declared attribution artifact; ≥40% → scale to €1M+ plan |
| 3 | **META conversion-lift study** | mROI ≈ 1.0 at current spend (i.e. META is at breakeven, not under water) | Platform conversion-lift (ghost ads) on DE, 4–6 weeks | **Aug** | Lift-based iROAS <1 → freeze META at H1 level, shift to TikTok/reserve; ≥1 → hold plan |
| 4 | **TikTok scaling stress test** | mROI stays ≥1.0 up to +50% spend (MMM says it falls to 0.63) | Geo-lift: +50% spend in test regions vs control, 6 weeks | **mid Aug–Sep** (after #3 so PS geos aren't double-treated) | mROI at scale ≥1 → deploy reserve into TikTok for Q4; <0.8 → hold TikTok at H1+20% |
| 5 | **Affiliate coupon-leak audit + commission holiday** | A meaningful share of affiliate revenue is last-click coupon poaching | Analytics audit + 2-week commission pause on coupon publishers in a segment | **Sep** | Sales unchanged during pause → restructure commissions before scaling |
| 6 | **YouTube paid staged scale-up** | The 37× iROAS is a small-base artifact but the channel clears mROI 1 at moderate spend | Budget doubling in capped steps with geo control (built into the €1.5M allocation) | **Jul–Oct, continuous** | Each step must hold lift-implied mROI ≥1.5 to unlock the next |

**Sequencing & interference rules:**
- #1 (creator holdout) and #2 (search geo) run in parallel — different mechanisms and different randomisation units; monitor search-volume contamination in #2 regions from #1's social buzz reduction (direction would bias *against* finding search incrementality, i.e. conservative).
- #3 and #4 both manipulate Paid Social in DE geos → run **sequentially**, not simultaneously.
- **Test freeze from Nov 1** (Black Week + Christmas): no holdouts during peak; peak-season response data feeds the next MMM refresh instead.
- **Decision gate: end of September** — Q4 budgets (incl. the €2.5M reserve) reallocate based on tests #1–#4; results also become priors/calibration constraints for MMM v6.

---

## Task 3 — Strategic Reflection

### 3.1 Assumptions I had to make (with confidence levels)

| Assumption | Confidence |
|---|---|
| The €35M must actually be spent in media (no option to bank it) | Low — this is the assumption I'd challenge first |
| MMM response *curves* (saturation shapes) extrapolate to ~2× current spend | **Low** — the model was fit on €48M/125wk; €35M/26wk is far outside the training range |
| Channel *ranking* by mROI is directionally right even if point estimates aren't | Medium-high — this is exactly what the memo claims survives holdout R² 0.29 |
| DE attribution data generalises to the full budget scope (memo/MMM is DE-focused; the task doesn't state geography) | Medium — I planned as if this is the DE budget |
| Influencer contract floors allow a mix shift but not a fast exit | Medium — memo hints at floor-driven cuts (limitation #3) |
| H1 run-rates from the memo (§5.2) are accurate actuals | High |
| Brand's true ROI exceeds the measured 1.04× (8-week window) | Medium-high — standard MMM limitation, memo agrees |

### 3.2 Unclear or unusual data points, and how I handled them *(code [§5.3](analysis.ipynb#sec53))*

1. **The memo optimises €16.1M while the task hands me €35M.** The single most important inconsistency — the "recommendation" I was given as primary input argues against my budget. Handled by treating €35M as a constraint, phasing toward Q4, and reserving budget for measurement (see §0).
2. **YouTube Paid Social: iROAS 37×, mROI "20 (capped)" on €0.07M spend (0.5% of PS).** A €2.82M contribution from €70K spend is not credible; the model itself caps it and flags LOW confidence. Handled: treated as "promising, unproven"; staged test allocation only.
3. **MMM vs attribution head-on conflict:** MMM ranks Influencer worst (mROI 0.40) while attribution shows Influencer as the volume-and-efficiency star (20K NCs at €56 CAC, CLV6M/CAC ≈ 100%); conversely TikTok looks great in MMM (1.35) and terrible in attribution (CAC €203, CLV/CAC 25%). Classic last-touch vs econometrics disagreement (influencers harvest credit; TikTok creates demand others harvest). Handled: this became Test #1 and #4 — I deliberately did not pick a side with money beyond what the constraint forces.
4. **"DE other (paid)": CAC €10.4 in May, 7K NCs from €73K.** Implausibly cheap and unexplained (branded search? retargeting? misattributed organic?). Excluded from decisions; flagged for definition.
5. **Attributed CAC is drifting up while volume drops** (DE total: 48K NCs @ €55.5 in March → 39K @ €54.6 in May, with April worse at €63): demand softening or attribution churn — matters for the H2 NC targets.
6. **CLV6M/CAC < 100% for META (46–56%) and TikTok (25–39%)** — on attribution numbers, paid social doesn't pay back in 6 months. Either CLV is longer-tailed than 6 months or paid social is structurally unprofitable on acquisition — a question the MMM answers differently (incremental revenue, not attributed NC). Handled: noted as a reason mROI, not CAC, drives my allocation.
7. **Brand iROAS 1.04× on an 8-week window** — a lower bound by construction. Handled: didn't cut brand below the fixed envelope, and biased the sub-mix toward the strongest measured line (Events 1.74×).
8. **Holdout R² = 0.29 / WAPE 27.7%** passes the stated gate but means point projections (±) are wide. Handled: used the model for *ranking and direction*, never for revenue promises; the memo's €57M revenue projection should carry explicit bands.
9. **Influencer L2 "floors bind"** (limitation #3): the per-platform cut is contract-driven, not optimal — so the L2 influencer numbers describe feasibility, not desirability. Handled: made contract renegotiation an explicit action item.

### 3.3 Missing business context I'd ask for

- **Why €35M?** The growth target, margin expectations, and whether "spend it all" beats "return €10M" — the plan changes completely depending on the answer.
- **Gross margin & unit economics per category** — MMM optimises revenue; the business optimises contribution. A revenue-optimal plan can be margin-destructive (especially promo-heavy Q4).
- **Geographic scope** — is the €35M DE-only (matching the MMM) or group-wide incl. FR/UK? The MMM covers only DE spend.
- **CLV beyond 6 months + cohort quality by channel** — to reconcile the CLV6M/CAC <1 problem and set true CAC ceilings.
- **Influencer contract structure** — which floors, until when, penalty costs; determines how fast the mix shift can happen.
- **Capacity/ops constraints** — Q1's syrup production gap showed fulfilment can throttle marketing; Black Week plans need ops sign-off (memo limitation #6 agrees).
- **Competitive & pricing calendar for H2** — a competitor's Black Week aggression changes the promo/media split.
- **New-product roadmap for H2** — launches (like syrup in Q1) create demand spikes the MMM attributes partly to media; knowing the calendar avoids over-crediting channels.
- **Attribution methodology** ("attributed NC" = last click? data-driven?) and the definition of "DE other (paid)".
- **Whether promo (€25M contribution, 18% of revenue) is inside or outside my €35M** — promo is modeled as a separate driver; if promo budget competes with media budget, the allocation question changes shape.

---

*Time spent: ~1.5 hours (desk analysis of the memo + write-up). No code needed; all figures from the memo.*
