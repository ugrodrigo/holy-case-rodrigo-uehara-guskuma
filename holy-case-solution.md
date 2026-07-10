# HOLY Marketing Analyst Case — Full Solution

*[Rodrigo Uehara Guskuma](https://www.linkedin.com/in/rodrigo-guskuma/) · July 2026*

**Code:** all computations behind this document live in `analysis.ipynb`, rendered with outputs on GitHub:
[https://github.com/ugrodrigo/holy-case-rodrigo-uehara-guskuma/blob/main/analysis.ipynb](https://github.com/ugrodrigo/holy-case-rodrigo-uehara-guskuma/blob/main/analysis.ipynb)
— the *(code: analysis.ipynb §…)* tags throughout name the notebook section that computes each number.

| Contents | |
|---|---|
| [Part 1 — Q1: 5th BURRsday Syrup Campaign analysis](#part-1) | Launch analysis, briefing format |
| [Part 2 — Q2: H2 2026 budget, testing roadmap & strategic reflection](#part-2) | Based on the MMM memo + attribution data |
| [Appendix A — Further analysis beyond the brief](#appendix-a) | Seven additional analyses |
| [Appendix B — Method & project blueprint](#appendix-b) | Link to the full decision log |
| [Appendix C — Data quality review](#appendix-c) | Link to the full audit & additional info provided by Martijn |

---

<a id="part-1"></a>
# Part 1 — Q1: 5th BURRsday Syrup Campaign — Launch Analysis

*Briefing for the Head of Marketing · Data: Apr 1 – May 31, 2026 (DE/FR/UK Shopify) · All monetary figures are **net revenue** (the marketing team's steering metric — additional info provided by Martijn: item revenue + shipping − refunds − tax) and should be read as **relative** comparisons, per the dataset disclaimer.*

> **Data prep:** duplicate orders deduped, cancelled orders excluded (rule provided by Martijn: `refunded_value ≥ gross_revenue`), orphan line items and `10-00-42-002x` spare-part items dropped, delivery delay computed as `delivered_at − order_date`. Details, open questions and the additional info provided by Martijn in [Appendix C — Data Quality Review](#appendix-c).
>
> **Code:** every number in this document is produced by **analysis.ipynb** (in the repository, rendered with outputs on GitHub). Each section below carries a *(code: analysis.ipynb §…)* tag naming the analysis.ipynb section that computes it; the data-cleaning rules are in analysis.ipynb §2.

---

## TL;DR — the five things to know

1. **The launch worked as a demand event.** Campaign net revenue ran at **+118% per day vs. the pre-period** (+57% excluding launch day). Launch day alone did ~€3.1M — 11× a pre-campaign day — and half of all campaign bottle volume. *(details [§2.1](#q1-21), [§2.4](#q1-24) · code: analysis.ipynb §3.1, §3.4)*
2. **It was an existing-customer launch, not an acquisition launch.** Only **22.7%** of syrup-bottle orders came from first-time customers vs. 47.8% for non-syrup orders in the same window. The syrup bottle monetized the base; it did not (yet) bring new people in. *(details [§2.2](#q1-22), [further analysis §3](#fa-3) · code: analysis.ipynb §3.2, §6.3)*
3. **DE carried it**: 74% of bottle units, 57.0% of DE campaign orders contained a bottle (FR 30.5%, UK 22.4%). FR underperformed its size (+51% net revenue uplift vs. DE +184%). *(details [§2.3](#q1-23) · code: analysis.ipynb §3.1, §3.3)*
4. **The production gap was expensive.** Bottle orders placed in weeks 18–19 waited a **median 38–40 days** for delivery. Reorder rate drops with delay: **14.6% (delivered ≤4 days) → 5.4% (>14 days)** raw; a truncation-corrected check puts the penalty at **roughly a fifth to a quarter lower reorder likelihood (≈1,000 lost reorders — an imprecise estimate)** ([further analysis §6](#fa-6)). The delays were confined to the **POZ1 warehouse, which fulfils DE/FR** — NOT1 shipped on time throughout, but it serves only the UK, so warehouse and market are confounded ([further analysis §4](#fa-4)). *(details [§3.3](#q1-33), [further analysis §4](#fa-4), [further analysis §6](#fa-6) · code: analysis.ipynb §3.9, §3.10, §6.4, §6.6)*
5. **Early retention is real but shallow:** 10.1% of identifiable campaign bottle buyers re-bought syrup within the observed 4–6 week window (median 23 days to reorder), mostly via the May 12 3er pod bundles — evidence the refill model works. No cannibalisation of the core Energy business is visible. *(details [§4.1](#q1-41), [§4.2](#q1-42) · code: analysis.ipynb §3.10, §3.11, §3.12)*

---

## 1. Setting expectations

### KPIs I would track for a launch like this

| KPI | Why it matters |
|---|---|
| **Adoption / penetration** (% of orders containing the hero SKU; units) | Direct measure of whether the launch landed |
| **Incremental revenue uplift** vs. pre-campaign baseline (total and per market) | Separates "campaign grew the pie" from "campaign relabeled existing demand" |
| **New vs. existing customer mix** of hero-product buyers | Tells you whether the product acquires or monetizes |
| **AOV & attach rate** (pods, consumables, cross-category) | The syrup system is razor-and-blades: the bottle only pays off if consumables attach |
| **Repeat / reorder rate** of hero buyers (30/60/90-day cohorts) | The single most important long-term KPI for a consumable ecosystem |
| **Fulfilment SLA** (days to delivery, % delayed) | Launch experience drives second purchase; we can measure it directly |
| **Category mix shift / cannibalisation** | Guards the core Energy business |
| **CAC / media efficiency** *(not measurable here — no spend data)* | Needed to call ROI, flagged as an open ask |

### What "good" looks like

- **End of campaign:** high penetration among existing customers (the launch audience), strong pods attach (validates the system), demand sustained after the launch-day spike rather than a one-day novelty, and no meltdown in fulfilment.
- **+30 days:** the KPI shifts from adoption to **repeat**. Good = a double-digit share of bottle buyers back for refills within 30 days, refill volume replacing launch hype as the demand driver, and no lasting dent in Energy sales. A first-ever product has no benchmark, so the honest framing is: *set the benchmark now, manage the curve* (which is what the May 12 3er bundle did).

---

## 2. Campaign analysis

<a id="q1-21"></a>
### 2.1 Baseline & uplift *(code: analysis.ipynb §3.1)*

![Daily net revenue](charts/daily_revenue.png)

| Period | Days | Orders/day | Net €/day | AOV (net) | First-order share |
|---|---|---|---|---|---|
| Pre (Apr 1–18) | 18 | 4,045 | €268k | €66.2 | 46.9% |
| **Campaign (Apr 19–May 4)** | 16 | **7,828 (+94%)** | **€585k (+118%)** | **€74.7 (+13%)** | 36.6% |
| Post (May 5–31) | 27 | 5,392 (+33%) | €372k (+39%) | €69.1 | 40.7% |

- Excluding launch day, the campaign still ran **+50% orders / +57% revenue per day** — the uplift was not just one spike. *(code: analysis.ipynb §3.1)*
- The post-period stays ~39% above pre, but is **contaminated upward** by the May 12 3er launch, a May 18 sampling giveaway and the May 27 Summer Cocktail launch — read it as "elevated, partly for other reasons", not as pure campaign halo. *(code: +39% in analysis.ipynb §3.1; event evidence §1.5 and §3.12; all three annotated in the §4.1 chart)*
- **Robustness:** the pre-period contains Easter (Apr 3–6) and the campaign contains May 1 — re-basing on an Easter-adjusted baseline moves the uplift only from +118% to **+114%** (+54% excl. launch day *and* May 1). Zero-revenue sample orders (11.7k in the post-period, mostly May 18) inflate post *order counts* (+33% → **+24%** when excluded) but not revenue (+39% either way). *(code: analysis.ipynb §3.1 sensitivity checks)*
- **Interpretation (hypothesis):** launch-day concentration (28% of campaign orders in one day) points to a well-primed audience — likely CRM/community activation. That same concentration implies part of the uplift is **demand pull-forward** from the base rather than net-new consumption; separating the two needs a longer horizon or a control market. This analysis can only quantify the **demand uplift** the campaign generated; If it could be paired with the campaign's spend (media, CRM, influencer fees) it would be possible to convert these numbers into true **ROI/ROAS** — this would be the first data request I'd make to complete the launch evaluation. *(code: analysis.ipynb §3.1)*

<a id="q1-22"></a>
### 2.2 Existing vs. new customers *(code: analysis.ipynb §3.2)*

- Campaign first-order share **fell** to 36.6% (pre: 46.9%) — the extra demand was disproportionately **existing customers**. *(code: analysis.ipynb §3.2)*
- Bottle orders: **22.7% new** vs. 47.8% for non-bottle orders in the campaign window. *(code: analysis.ipynb §3.2)*
- *Reliability note:* these splits rest on the `is_first_order` flag. The audit initially reported 1,583 customers with two "first" orders; on re-review that was a computation artifact (null `customer_id`s counted as duplicates of each other) — after dedupe **no** customer carries two "first" orders, so the flag is clean. The remaining identity caveat is the 3,450 orders with no `customer_id` at all (data quality review §2.8) — orders of magnitude too small to close the 22.7% vs 47.8% gap. *(code: analysis.ipynb §1.1)*
- **What the data shows:** the syrup launch activated the base. **My interpretation:** that's the right sequencing for a new format (fans forgive teething problems), but H2 needs an acquisition angle for syrup — currently it doesn't pull new customers. *(code: analysis.ipynb §3.2, §6.3)*

<a id="q1-23"></a>
### 2.3 Market breakdown *(code: analysis.ipynb §3.3)*

![Market breakdown](charts/market_breakdown.png)

| Market | Net revenue uplift (campaign vs pre, €/day) | Bottle units share | Bottle penetration of campaign orders |
|---|---|---|---|
| DE | **+184%** | 74% | 57.0% |
| UK | +107% | 5% | 22.4% |
| FR | **+51%** | 21% | 30.5% |

FR is the underperformer relative to its base size (FR is ~44% of pre-period net revenue but took only 21% of bottle units). Hypotheses to check with the team: weaker localisation/creator support, price sensitivity, or the campaign being DE-centric by design.

<a id="q1-24"></a>
### 2.4 Daily trend *(code: analysis.ipynb §3.4)*

![Daily bottle units](charts/daily_bottles.png)

Launch day sold **31,723 bottles — 50% of total campaign bottle volume** — then demand decayed over the following week (5.4k → 1.8k/day) and stabilised at ~1,000–1,600/day from Apr 25 (average ~1,430/day over the final 10 days; ~2,100/day across the whole post-launch stretch including the decay days), settling at ~720/day on average post-campaign with a clear bump on May 12 (3er launch). The demand curve is a classic hype-then-baseline launch; the late-May run-rate (~500–600 bottles/day) is the number to plan production against.

<a id="q1-25"></a>
### 2.5 Category mix *(code: analysis.ipynb §3.5)*

Share of net revenue by category:

| Category | Pre | Campaign | Post |
|---|---|---|---|
| Syrup Bottle + Syrup Bundle (pods) | 0% | **34.2%** | 13.4% |
| Energy (bundle + sachet + box) | 29.2%* | 21.8% | 28.6%* |
| Mixed Sachetbox | 27.3% | 14.7% | 17.3% |
| Hydration Bundle | 15.3% | 10.5% | 14.3% |
| Iced Tea Bundle | 12.3% | 9.0% | 11.0% |

*\*Energy rows shown as the share of the three energy categories combined; full table in analysis.ipynb §3.5.*

The syrup franchise instantly became the #1 category during the campaign — in net terms the standard-VAT bottle weighs slightly less than the food-VAT consumables, so the **Energy Bundle** category edges the Syrup Bottle as the largest *single* category (20.2% vs 18.8%), but bottle + pods combined lead clearly at 34.2% — and it **retains ~13% of revenue after the campaign**, a real second leg, not a stunt. Energy's *share* dipped during the campaign but its **absolute net €/day grew** (€78.3k pre → €127.6k campaign → €106.7k post), so the dip is denominator effect, not decline (see cannibalisation, [§4.2](#q1-42)).

---

## 3. Products deep dive

<a id="q1-31"></a>
### 3.1 Adoption & buying behaviour *(code: analysis.ipynb §3.6, §3.7)*

- **44.7% of all campaign orders (55,982 of 125,252) contained a syrup bottle**; 63,356 bottles sold in 16 days. *(code: analysis.ipynb §3.6)*
- Flavour split: **Darko 49% · Syru 34% · Raptor 17%** — consistent enough that no flavour flopped, skewed enough to plan production toward Darko. *(code: analysis.ipynb §3.6)*
- **Every bottle was sold as part of a fixed bundle** (100% `is_bundle`) that included the 10er pods (99.8%) and usually a sticker (81%). Those "attach rates" are therefore **offer architecture, not customer choice** — the behavioural signals are that standalone bottles effectively weren't sold, only 94 pod packs went out *without* a bottle, and every owner starts the refill loop (§4.1). *(code: analysis.ipynb §3.6)*
- 88% of bottle orders took exactly one bottle (mean 1.13) — one bottle per household; multi-bottle gifting didn't materialize. *(code: analysis.ipynb §3.7)*
- **Bottle orders spend more:** AOV (net) €81.3 vs €69.4 for campaign non-bottle orders (+17%; the premium is larger in gross terms, +23% — the bottle is standard-VAT hardware while consumables carry food VAT). *(code: analysis.ipynb §3.7)*

<a id="q1-32"></a>
### 3.2 Cross-purchase *(code: analysis.ipynb §3.8)*

Among bottle orders (besides the bundled pods/sticker): **30% added a Shaker, 24% Merch, 16% an Energy tub**, 9% Iced Tea, 9% Hydration. Only 0.1% bought the bottle bundle alone. Syrup buyers are engaged multi-category customers — supports the "monetize the fans" read.

<a id="q1-33"></a>
### 3.3 Delivery delay impact — the costliest lesson of the launch *(code: analysis.ipynb §3.9)*

Bottle orders were dramatically slower than the rest of the shop:

| | Median delay | % > 7 days | % > 14 days |
|---|---|---|---|
| Campaign bottle orders | **8 days** | **51.6%** | **29.5%** |
| Campaign non-bottle orders | 4 days | 14.7% | 0.7% |

By order week, bottle orders placed in **weeks 18–19 (Apr 27 – May 10) waited a median 38–40 days** — the production gap hit customers who ordered late in the campaign; service recovered to ~5 days by week 20.

![Delay vs reorder](charts/delay_vs_reorder.png)

Reorder rate falls monotonically with the delay on the *first* bottle order: **14.6% → 11.1% → 9.7% → 5.4%**. Holding the ≤4-day rate as the counterfactual, the naive estimate is **~2,400 lost reorder customers (~4.4pp of cohort retention)**.
*Correction (see [further analysis §6](#fa-6)):* part of this gradient is calendar truncation — late-delivered customers had fewer days left to reorder before the data ends. A fixed 21-day-after-delivery window puts the **penalty at roughly a fifth to a quarter lower reorder likelihood, ≈1,000 lost reorders** — material, about half the naive read, though imprecisely estimated: the corrected gradient is not fully monotonic, so composition effects remain.

---

## 4. After the campaign

<a id="q1-41"></a>
### 4.1 Retention & reorders *(code: analysis.ipynb §3.10, §3.12)*

Of **54,127 identifiable campaign bottle buyers**:

- **10.1% (5,470) re-purchased syrup by May 31** (window: 4–6 weeks); median time to reorder **23 days** — consistent with a ~3-week consumption cycle for the bundled 10er pods. *(code: analysis.ipynb §3.10)*
- **Internal benchmark:** campaign buyers of the core Energy category re-bought energy at **10.4%** in the same window, once bottle buyers are excluded from the benchmark cohort to keep it independent (they are ~30% of it and repeat at 15.3% — the same engaged multi-category fans; 11.8% unadjusted). Syrup's 10.1% first-cohort repeat, achieved despite the delivery crisis, is therefore **essentially at core-category level** (identical campaign → May 31 clock, same window truncation). *(code: analysis.ipynb §3.10)*
- What they reordered: **3er pod bundles 3,992 · bottles 2,072 · 10er pods 1,978** (overlapping) — overwhelmingly **consumable refills**: the 3er is a *pod* bundle, not more hardware (naming confirmed against the product master). The May 12 3er launch was well-timed against the consumption cycle and immediately became the main reorder vehicle (3,365 orders on day one). *(code: analysis.ipynb §3.10, §3.12)*
- 3er bundle buyers since May 12: 10,215 orders, only **9.4% first-time customers**, and **42.4% verifiably owned a bottle already** — it is functioning as a retention product, as intended. (The other ~58% may have bought bottles before Apr 1, via another channel, or are gift buyers — worth checking, and one of my interviewer questions.) *(code: analysis.ipynb §3.12)*
- Reorder flavour ranking by units (Peach and Cola Ice Pop neck-and-neck at ~15% each, Green Apple third) is the first read on which refill flavours to scale. *(code: analysis.ipynb §3.12)*

<a id="q1-42"></a>
### 4.2 Cannibalisation risk *(code: analysis.ipynb §3.11)*

**No evidence of cannibalisation so far — if anything the opposite:**

- Energy absolute net revenue per day *rose* pre → post (+36%). *(code: analysis.ipynb §3.5)*
- Cohort comparison: existing customers who bought a bottle grew their energy spend/day **+73%** pre→post, vs **+51%** for existing campaign customers who didn't buy a bottle. *(code: analysis.ipynb §3.11)*

**Interpretation with caveats:** syrup looks complementary (different consumption occasion) rather than substitutive. But the post window is short, seasonal, and boosted by other launches; the honest verdict is "no early warning signs", to be re-checked on a 90-day horizon — the real risk is *share-of-stomach* over months, not weeks. Note also that both cohorts condition on a campaign purchase, but bottle buyers *self-selected* as the most engaged fans — read the +73% vs +51% contrast as the absence of a warning sign, not as a causal effect size.

<a id="q1-43"></a>
### 4.3 What worked / what to fix / open questions

**Worked:** launch-day activation of the base (28% of campaign orders in one day); bundle architecture (100% pods attach = every bottle owner starts the refill loop); the May 12 3er bundle timed to the refill cycle; a durable post-campaign syrup baseline (~13% of revenue).

**Needs improvement:**
1. **Fulfilment readiness** — the production gap measurably burned early retention (~1,000 reorders on the corrected estimate, [further analysis §6](#fa-6)) and it was confined to POZ1, the DE/FR warehouse ([further analysis §4](#fa-4) — note the warehouse/market confound); late-campaign buyers had a first experience of waiting 5+ weeks.
2. **FR performance** — +51% uplift vs DE's +184%; diagnose localisation/creator mix before H2.
3. **New-customer angle** — syrup didn't acquire; consider a syrup-led acquisition offer now that the base is saturated with bottles.

**Couldn't answer with this data (and what I'd need):**
- **Campaign ROI** → media spend & channel attribution for the window.
- **True incrementality of launch-day demand** (pull-forward vs. net-new) → longer pre-period / prior-year baseline.
- **Retention beyond 4–6 weeks & pods consumption rate** → June+ orders; subscription data if any.
- **Margin** impact of the bundle discount → COGS/discount data (net revenue in the extract is unreliable — see data review).
- Whether **delivery-delay victims** should get a win-back voucher (I'd A/B it — the ~15.8k identifiable customers who waited >14 days are a named, reachable audience).

---

## 5. Further analysis — beyond the brief's guiding questions

Seven additional analyses that sharpen — and in one case revise — the findings above are documented separately in **[Appendix A — Further Analysis](#appendix-a)**: refunds & cancellations, the May 18 sampling giveaway's conversion, new-customer cohort quality, the warehouse split of the delivery delays, the habit-formation effect, the truncation-corrected delay estimate (which cut the lost-reorders figure from ~2,400 to ~1,000), and a refill-demand planning number. *(code: analysis.ipynb §6)*

---

*Time spent: ~5 hours on Q1 (audit, analysis, charts, further analyses, write-up). Project total including Q2, the re-runs on Martijn's additional info and the adversarial self-review: ~7 h — full time log in the project blueprint. Fully scripted in Python/pandas for reproducibility.*


---

<a id="part-2"></a>
# Part 2 — Q2: H2 2026 Sales Budget — Allocation, Testing Roadmap & Strategic Reflection

*Inputs: MMM v5.3.1 memo (Section A) + DE attribution data Mar–May 2026 (Section B). Constraints: €35M total · €4M brand (fixed) · €800K TV locked in Q3 · €31M flexible performance budget.*

> **Code:** the memo PDF extraction, its tables transcribed as data, and every derived number in this document are in **analysis.ipynb §5** — PDF extraction in §5.1, the memo tables in §5.2, derived analysis & MMM-vs-attribution conflicts in §5.3, allocation checksums in §5.4.

---

## 0. The elephant in the room (read this first) *(code: analysis.ipynb §5.3)*

The MMM team's own optimised plan spends **€16.1M** in H2 — the task asks me to allocate **€35M**, i.e. **2.2× the model-optimal level** and ~1.8× the H1 run-rate (~€19.3M/half). The MMM says META's marginal ROI is already ~0.99 at *current* spend and TikTok's drops to 0.63 at just +50% spend. **At €31M of performance spend, the marginal euros are, per the model, value-destructive.**

I therefore treat the €35M as a hard constraint (growth/share-taking mandate, investor commitment, etc.) and build the plan that **minimises marginal-ROI damage, weights spend toward Q4** (Black Week/Christmas, when response curves support more spend), and **converts part of the budget into measurement** so the next planning cycle isn't flying blind. But my first recommendation as an analyst is explicit: *if the €35M is negotiable, right-size media to ~€20–24M and put the remainder into retention/CRM, promo depth, or reserves.*

---

## Task 1 — H2 Budget Allocation

### 1.1 Allocation logic

1. **Rank by marginal ROI (mROI), not average iROAS** — iROAS rewards history; mROI prices the next euro. Ranking: Google 4.46 > Affiliate 1.78 > Paid Social 1.50 (TikTok 1.35 / META 0.99) > Brand 0.44 ≈ Influencer 0.40.
2. **Scale the small high-mROI channels first** (Google, Affiliate) — but capped, because the memo itself flags their numbers as attribution-assisted; scaling is gated by incrementality tests.
3. **Paid Social takes the largest share**, tilted to TikTok early and META for depth — accepting that both saturate below mROI 1 at this budget.
4. **Influencer is restructured, not just cut.** The MMM says the *mix* is the problem (YouTube Inf. 0.30 vs Podcast 0.71 / TikTok Inf. 0.76). Because €31M must be deployed, Influencer keeps a substantial envelope, shifted toward the high-mROI platforms as far as contract floors allow. Why not cut it to the contractual floor and hold a bigger reserve instead? Paid Social is already deep into saturation at its €17M envelope (the memo shows mROI < 1 at far smaller increases), the reserve is sized to what Q4 can actually deploy well, and the floors (memo limitation #3) commit much of the influencer spend regardless — the controllable lever is the *mix*, which this plan shifts hard.
5. **Hold a testing & flex reserve** — with model holdout R² of 0.29, buying information is worth more than the last saturated media euro. The reserve deploys in Q4 into whatever the Q3 tests validate.
6. **Phase Q3 < Q4** (≈ 40/60): H2 contains Black Week + Christmas; saturation ceilings are higher when demand is elevated.

### 1.2 Channel-by-channel plan (H2 totals) *(code: analysis.ipynb §5.4)*

| Channel | H1 actual (≈26wk) | **H2 plan** | vs H1 | Logic |
|---|---|---|---|---|
| **Paid Social** | €5.9M | **€17.0M** | +186% | Largest share; least-bad saturation profile. META €10.0M (workhorse; mROI ~0.99 at *current* spend — at 2.2× current the model implies a marginal euro well below breakeven, accepted as the least-bad home for constrained budget and sanity-checked by the Aug lift study), TikTok €5.5M (momentum; runs at the memo's +50% level in Q3, the remainder deploys in Q4 only if the stress test clears mROI ≥1), YouTube paid €1.5M (scaling test of the "37× iROAS" signal — treated as unproven, capped, staged) |
| **Influencer** | €7.0M | **€9.6M** | +37% | Forced above optimal by the budget constraint. Mix shift: YouTube Inf. share cut from ~50% toward Podcast / TikTok Inf. / Instagram (mROI 0.5–0.8); renegotiate or exit floor-driven contracts; whitelisting/paid-amplification bridges Influencer → Paid Social |
| **Google Search** | €0.15M | **€1.0M** | ~6.7× | Highest mROI, tiny base. Phase: 2× run-rate in Q3 (the memo's controlled test), scale to plan in Q4 only if the brand-keyword holdout confirms incrementality. Note: the memo's Google mROI (4.46) *exceeds* its average iROAS (4.09). Two readings: benign — at 1.4% spend share Google may sit in the convex low-spend region of the model's S-shaped response curves, where marginal > average is expected — or attribution assistance inflating the fitted curve. The holdout test discriminates between them; until then the scale-up stays gated |
| **Affiliate** | €0.46M | **€0.9M** | ~2× | mROI 1.78; scale moderately; audit coupon-poaching first |
| **Testing & flex reserve** | — | **€2.5M** | — | Funds incrementality tests + Q4 reallocation into validated winners (likely destinations: TikTok scale-up, Google, or promo support in Black Week) |
| **Performance subtotal** | €13.5M | **€31.0M** | | |
| **Brand (fixed)** | €5.8M | **€4.0M** | −31% | See split below |
| **TOTAL** | ~€19.3M | **€35.0M** | +81% | |

### 1.3 Brand envelope (€4.0M) *(code: analysis.ipynb §5.4)*

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

### 1.4 Quarterly phasing *(code: analysis.ipynb §5.4)*

| | Q3 | Q4 | Rationale |
|---|---|---|---|
| Performance | €12.0M | €19.0M | Black Week + Christmas raise saturation ceilings; Q3 runs the tests that de-risk Q4 |
| Brand | €2.2M | €1.8M | TV lock + events season in Q3; Q4 brand supports peak trading |
| **Total** | **€14.2M** | **€20.8M** | |

### 1.5 Why this plan carries no point revenue projection

Deliberately. The model's holdout R² is 0.29, and €35M per half is ~3.5× the average half-year media spend in its 125-week training window (€48M total) — H1 itself already ran ~1.9× above that long-run average — so a point forecast would extrapolate far outside fitted territory with false precision. Directional bounds only: *if the memo's baseline+promo scenario holds*, the outcome should not sit below its €57M projection (media effects are constrained non-negative and we spend strictly more — but the floor is conditional on non-media demand: the memo's own low non-media scenario lands at €53.8M), while a naive linear extrapolation of the memo's plan (€70M+) is an upper bound that saturation guarantees we will not reach. The honest deliverable is the ranking, the phasing and the tests — the Q3 experiments in Task 2 are what turn this allocation into a defensible Q4 forecast.

---

## Task 2 — Testing Roadmap

**Principle:** every €1M+ allocation decision that rests on a disputed number gets an incrementality test *before* the Q4 reallocation gate (end of September). The tests target exactly the three places where MMM and attribution disagree most.

| # | Test (priority order) | Hypothesis | Method | Timing | Decision trigger |
|---|---|---|---|---|---|
| 1 | **Influencer incrementality** — the biggest money on the most disputed number (MMM mROI 0.40 vs attribution CLV6M/CAC ≈ 100%) | ≥50% of attributed influencer NCs would have converted anyway | Creator-level holdout: pause a matched ~30% panel of DE creators for 6 weeks; measure regional/audience NC delta vs control panel (geo-split is impractical for national creators) | **Jul–mid Aug** | Incremental CAC > €85 (≈1.5× attributed) → cut Influencer to floor in Q4, reserve moves to PS/Google. Incremental CAC ≤ CLV6M → restore mix-shifted budget |
| 2 | **Google Search brand-keyword holdout** — cheap, fast, independent | ≥60% of brand-search conversions are non-incremental | Pause brand keywords in a matched set of DE regions (geo split) for 4 weeks | **Jul** (parallel to #1 — different mechanism, minimal interference) | True incrementality <40% → cap Google at 2× run-rate, iROAS 4.09 declared attribution artifact; ≥40% → scale to €1M+ plan |
| 3 | **META conversion-lift study** | mROI ≈ 1.0 at current spend (i.e. META is at breakeven, not under water) | Platform conversion-lift (ghost ads) on DE, 4–6 weeks | **Aug** | Lift-based iROAS <1 → freeze META at H1 level, shift to TikTok/reserve; ≥1 → hold plan |
| 4 | **TikTok scaling stress test** | mROI stays ≥1.0 up to +50% spend (MMM says it falls to 0.63) | Geo-lift: +50% spend in test regions vs control, 6 weeks | **mid Aug–Sep** (after #3 so PS geos aren't double-treated; read-out lands in the last week of Sep — zero slack against the gate, so if it slips the reserve defaults to Google/promo rather than TikTok) | mROI at scale ≥1 → deploy reserve into TikTok for Q4; <0.8 → hold TikTok at H1+20% |
| 5 | **Affiliate coupon-leak audit + commission holiday** | A meaningful share of affiliate revenue is last-click coupon poaching | Analytics audit + 2-week commission pause on coupon publishers in a segment | **Sep** | Sales unchanged during pause → restructure commissions before scaling |
| 6 | **YouTube paid staged scale-up** | The 37× iROAS is a small-base artifact but the channel clears mROI 1 at moderate spend | Budget doubling in capped steps with geo control (built into the €1.5M allocation) | **Jul–Oct, continuous** | Each step must hold lift-implied mROI ≥1.5 to unlock the next |

**Sequencing & interference rules:**
- #1 (creator holdout) and #2 (search geo) run in parallel — different mechanisms and different randomisation units; monitor search-volume contamination in #2 regions from #1's social buzz reduction (direction would bias *against* finding search incrementality, i.e. conservative).
- #3 and #4 both manipulate Paid Social in DE geos → run **sequentially**, not simultaneously.
- #1 and #3 have a potential **mid-August seam** (the creator holdout may still be live when the META lift starts). Preferably start #3 after #1 ends; if they must overlap, user-level ghost-ad randomisation is robust to the background influencer shift, but the measured META lift is then conditional on a reduced-influencer environment — note it in the read-out.
- **Holdout cost:** influencer contract floors mean some paused creators are paid regardless — the idle-floor cost of test #1 is budgeted from the €2.5M reserve and needs media-team/legal sign-off before launch; where possible the panel prioritises unfloored creators and contracts in their renewal window.
- **Seasonality caveat:** all tests read out in the Q3 low season; incrementality measured in Jul–Sep may not extrapolate to Black Week demand. Q4 reallocations therefore move in the *direction* the tests indicate but are capped in magnitude, and peak-season response is measured by the MMM v6 refresh (with the test results as calibration priors) rather than extrapolated from Q3 lifts.
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

### 3.2 Unclear or unusual data points, and how I handled them *(code: analysis.ipynb §5.3)*

1. **The memo optimises €16.1M while the task hands me €35M.** The single most important inconsistency — the "recommendation" I was given as primary input argues against my budget. Handled by treating €35M as a constraint, phasing toward Q4, and reserving budget for measurement (see §0).
2. **YouTube Paid Social: iROAS 37×, mROI "20 (capped)" on €0.07M spend (0.5% of PS).** A €2.82M contribution from €70K spend is not credible; the model itself caps it and flags LOW confidence. Handled: treated as "promising, unproven"; staged test allocation only.
3. **MMM vs attribution head-on conflict:** MMM ranks Influencer worst (mROI 0.40) while attribution shows Influencer as the volume-and-efficiency star (20K NCs at €56 CAC, CLV6M/CAC ≈ 100%); conversely TikTok looks great in MMM (1.35) and terrible in attribution (CAC €203, CLV/CAC 25%). Classic last-touch vs econometrics disagreement (influencers harvest credit; TikTok creates demand others harvest). Handled: this became Test #1 and #4 — I deliberately did not pick a side with money beyond what the constraint forces.
4. **"DE other (paid)": CAC €10.4 in May, 7K NCs from €73K.** Implausibly cheap and unexplained (branded search? retargeting? misattributed organic?). Excluded from decisions; flagged for definition.
5. **Attributed NC volume is volatile and net-declining** — DE total 48K (Mar) → 33K (Apr) → 39K (May), a dip with partial recovery rather than a straight slide — while blended CAC spiked to €63 in April before settling at €54.6: demand softening, seasonality, or attribution churn — matters for the H2 NC targets.
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

*Time spent: ~1.5 hours (part of the ~7 h project total — see the blueprint time log). All memo figures are transcribed and re-derived in analysis.ipynb §5.*


---

<a id="appendix-a"></a>
# Appendix A — Further Analysis — Beyond the Brief's Guiding Questions

*Seven analyses the case didn't ask for but that sharpen — and in one case honestly **revise** — the findings of the [Q1 campaign analysis](#part-1). All numbers computed in analysis.ipynb §6.*

<a id="fa-1"></a>
## 1. Refunds & cancellations *(code: analysis.ipynb §6.1)*
Under the cancellation rule provided by Martijn (full refund), **bottle orders almost never cancelled: 0.02% vs 0.36%** for non-bottle orders in the campaign window — despite 5-week waits, customers kept their orders. Partial refunds are more common on bottle orders (1.3% vs 0.3%), consistent with goodwill/shipping credits on delayed orders, and refund incidence does **not** rise with delay (0.2–0.5% across delay buckets). **The production gap's cost was retention, not immediate revenue give-back.** *Note: this finding flipped direction once the refund-based cancellation rule replaced the earlier shipment-status proxy — the "cancelled" shipment rows on bottle orders were replacement re-shipments, not customer cancellations.*

<a id="fa-2"></a>
## 2. The May 18 sampling giveaway barely converted *(code: analysis.ipynb §6.2)*
The 5.4k new customers who received free samples on May 18 converted to a **paid order within 13 days at just 0.37%**, vs **4.8%** repeat-paid for organically acquired new customers in a matched window. The two cohorts sit at different funnel stages (a free-sample taker vs. someone who already paid), so the ~13× gap overstates the verdict — but near-zero immediate conversion still means the giveaway must be judged as **lead generation** (i.e., on the CRM follow-up to those 5.4k contacts), not as a sales driver. *(Caveat: 13 days is short.)*

<a id="fa-3"></a>
## 3. Campaign-acquired new customers are weaker, not stronger *(code: analysis.ipynb §6.3)*
Fixed 21-day repeat window: campaign new customers **with** a bottle repeat at **4.5%**, vs 8.5% for campaign new customers without one and 9.3% for pre-period new customers. Even accounting for the ~23-day syrup refill cycle and the delivery delays, this further weakens syrup as an acquisition tool: it pulled few new customers (Q1 briefing §2.2) and the ones it pulled repeat less — consistent with hype/gift buyers.

<a id="fa-4"></a>
## 4. The production gap was a single-warehouse problem *(code: analysis.ipynb §6.4)*
Bottle orders fulfilled from **POZ1 took a median 8 days (mean 13)**; **NOT1 shipped bottles in ~4 days throughout** — same as its normal service. One structural caveat: **NOT1 fulfils only UK orders and POZ1 fulfils DE/FR**, so warehouse and market are fully confounded — "POZ1 problem" and "DE/FR problem" are the same statement in this data. The ops recommendation still sharpens from "fix production planning" to an EU/POZ1 stock-allocation fix, but attributing NOT1's clean record to warehouse operations (rather than separate UK stock allocation or its far lower volume, 5% of bottles) needs ops input before relying on cross-shipping in future launches.

<a id="fa-5"></a>
## 5. The launch re-activated dormant customers — and they stayed active *(code: analysis.ipynb §6.5)*
Existing customers who bought a bottle nearly **doubled their total net spend/day post-campaign (+89%)** while the matched control group *declined* (−5%). Notably, the bottle cohort was **less active pre-campaign** despite being larger — the launch pulled dormant fans back, and they kept ordering. *(Interpretation caveat: part of the lift is syrup refills themselves plus regression to the mean; a matched-activity control would isolate the pure habit effect.)*

<a id="fa-6"></a>
## 6. Honest revision: the true delay penalty is smaller than the raw gradient *(code: analysis.ipynb §6.6)*
The 14.6% → 5.4% gradient in the Q1 briefing §3.3 partly reflects **calendar truncation** (late-delivered customers had fewer days left to reorder before the data ends). Giving every customer the same **21-day clock starting at delivery** (deliveries ≤ May 10 only), the rates become **9.9% (0–4d) vs ~7.1–8.0% (delayed)** — a **~24% relative penalty**, or roughly **~1,000 lost reorders rather than the naive ~2,400**. Two honesty notes: the corrected gradient is **not monotonic** — the >14d bucket (which, restricted to deliveries by May 10, contains the most enthusiastic early launch buyers) sits at 8.0%, *above* the 8–14d bucket — so composition effects remain, and the penalty is best read as **"roughly a fifth to a quarter", not a point estimate**. The delay effect is real and material, but about half the naive read and imprecisely sized. The Q1 briefing's TL;DR #4 and §3.3 are stated with this correction applied.

<a id="fa-7"></a>
## 7. A refill-demand planning number *(code: analysis.ipynb §6.7)*
From well-served owners' steady-state behaviour: **per 1,000 bottle owners, expect ~140 refill orders (~500 pod packs) per month**. First-month estimate from the launch cohort — refine with June+ data — but it's the number production planning lacked when the gap happened.


---

<a id="appendix-b"></a>
# Appendix B — Method & Project Blueprint

The full method behind this document — per-phase workflow, design decisions with rationale, principles,
known limitations and the time log (~7 h) — is documented in
**[docs/project-plan-blueprint.md](https://github.com/ugrodrigo/holy-case-rodrigo-uehara-guskuma/blob/main/docs/project-plan-blueprint.md)**.


---

<a id="appendix-c"></a>
# Appendix C — Data Quality Review

The dataset audit — issue-by-issue findings, the cleaning rules applied to every number in this document
(dedupe · drop orphans · cancelled = `refunded_value ≥ gross` · spare-part SKUs excluded · net revenue as the
money metric), and the **additional info provided by Martijn** (revenue definitions,
cancellation rule, spare-part SKUs) — is documented in
**[docs/data-quality-review.md](https://github.com/ugrodrigo/holy-case-rodrigo-uehara-guskuma/blob/main/docs/data-quality-review.md)** (code: analysis.ipynb §1–2).
