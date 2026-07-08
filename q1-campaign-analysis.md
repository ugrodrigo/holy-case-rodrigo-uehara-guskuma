# Q1 — 5th BURRsday Syrup Campaign: Launch Analysis

*Briefing for the Head of Marketing · Data: Apr 1 – May 31, 2026 (DE/FR/UK Shopify) · All monetary figures are gross revenue and should be read as **relative** comparisons, per the dataset disclaimer.*

> **Data prep:** duplicate orders deduped, fully-cancelled orders excluded, orphan line items dropped, delivery delay computed as `delivered_at − order_date`. Details and open data questions in [data-quality-review.md](data-quality-review.md).
>
> **Code:** every number in this document is produced by **[analysis.ipynb](analysis.ipynb)** — each section carries a `(code §…)` tag pointing to the notebook section that computes it (cleaning rules in [§2](analysis.ipynb#sec2)).

---

## TL;DR — the five things to know

1. **The launch worked as a demand event.** Campaign revenue ran at **+124% per day vs. the pre-period** (+60% excluding launch day). Launch day alone did ~€3.4M — 6× a normal day — and half of all campaign bottle volume.
2. **It was an existing-customer launch, not an acquisition launch.** Only **22.9%** of syrup-bottle orders came from first-time customers vs. 47.9% for non-syrup orders in the same window. The syrup bottle monetized the base; it did not (yet) bring new people in.
3. **DE carried it**: 74% of bottle units, 56.8% of DE campaign orders contained a bottle (FR 30.3%, UK 22.3%). FR underperformed its size (+55% revenue uplift vs. DE +191%).
4. **The production gap was expensive.** Bottle orders placed in weeks 18–19 waited a **median 38–40 days** for delivery. Reorder rate drops with delay: **14.5% (delivered ≤4 days) → 5.4% (>14 days)** raw; after correcting for calendar truncation the causal penalty is **~25% lower reorder likelihood, ≈1,000 lost reorders** (§5.6). It was also a **single-warehouse (POZ1) problem** — NOT1 shipped bottles on time throughout (§5.4).
5. **Early retention is real but shallow:** 10.1% of identifiable campaign bottle buyers re-bought syrup within ~4 weeks (median 23 days to reorder), mostly via the May 12 3er pod bundles — evidence the refill model works. No cannibalisation of the core Energy business is visible.

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

### 2.1 Baseline & uplift *(code [§3.1](analysis.ipynb#sec31))*

![Daily gross revenue](charts/daily_revenue.png)

| Period | Days | Orders/day | Gross €/day | AOV | First-order share |
|---|---|---|---|---|---|
| Pre (Apr 1–18) | 18 | 4,046 | €287k | €70.9 | 46.9% |
| **Campaign (Apr 19–May 4)** | 16 | **7,798 (+93%)** | **€641k (+124%)** | **€82.3 (+16%)** | 36.8% |
| Post (May 5–31) | 27 | 5,383 (+33%) | €402k (+40%) | €74.7 | 40.8% |

- Excluding launch day, the campaign still ran **+49% orders / +60% revenue per day** — the uplift was not just one spike.
- The post-period stays ~40% above pre, but is **contaminated upward** by the May 12 3er launch, a May 18 sampling giveaway and the May 27 Summer Cocktail launch — read it as "elevated, partly for other reasons", not as pure campaign halo.
- **Interpretation (hypothesis):** launch-day concentration (28% of campaign orders in one day) points to a well-primed audience — likely CRM/community activation. Without media spend data we can measure *uplift*, not *ROI*.

### 2.2 Existing vs. new customers *(code [§3.2](analysis.ipynb#sec32))*

- Campaign first-order share **fell** to 36.8% (pre: 46.9%) — the extra demand was disproportionately **existing customers**.
- Bottle orders: **22.9% new** vs. 47.9% for non-bottle orders in the campaign window.
- **What the data shows:** the syrup launch activated the base. **My interpretation:** that's the right sequencing for a new format (fans forgive teething problems), but H2 needs an acquisition angle for syrup — currently it doesn't pull new customers.

### 2.3 Market breakdown *(code [§3.3](analysis.ipynb#sec33))*

![Market breakdown](charts/market_breakdown.png)

| Market | Revenue uplift (campaign vs pre, €/day) | Bottle units share | Bottle penetration of campaign orders |
|---|---|---|---|
| DE | **+191%** | 74% | 56.8% |
| UK | +109% | 5% | 22.3% |
| FR | **+55%** | 21% | 30.3% |

FR is the underperformer relative to its base size (FR is ~40% of pre-period revenue but took only 21% of bottle units). Hypotheses to check with the team: weaker localisation/creator support, price sensitivity, or the campaign being DE-centric by design.

### 2.4 Daily trend *(code [§3.4](analysis.ipynb#sec34))*

![Daily bottle units](charts/daily_bottles.png)

Launch day sold **31,594 bottles — 50% of total campaign bottle volume** — then decayed to a steady ~1,000–1,600/day for the rest of the campaign, settling at ~400–900/day post-campaign with a clear bump on May 12 (3er launch). The demand curve is a classic hype-then-baseline launch; the post-campaign bottle baseline (~500/day) is the number to plan production against.

### 2.5 Category mix *(code [§3.5](analysis.ipynb#sec35))*

Share of gross revenue by category:

| Category | Pre | Campaign | Post |
|---|---|---|---|
| Syrup Bottle + Syrup Bundle (pods) | 0% | **35.0%** | 13.8% |
| Energy (bundle + sachet + box) | 29.5%* | 21.5% | 28.6%* |
| Mixed Sachetbox | 27.3% | 14.5% | 17.2% |
| Hydration Bundle | 15.1% | 10.3% | 14.2% |
| Iced Tea Bundle | 12.2% | 8.7% | 10.8% |

*\*Energy rows shown as the share of the three energy categories combined; full table in the analysis script output.*

Syrup instantly became the #1 category during the campaign, and **retains ~14% of revenue after it** — a real second leg, not a stunt. Energy's *share* dipped during the campaign but its **absolute €/day grew** (€84.6k pre → €137.8k campaign → €115.2k post), so the dip is denominator effect, not decline (see cannibalisation, §4.2).

---

## 3. Products deep dive

### 3.1 Adoption & buying behaviour *(code [§3.6](analysis.ipynb#sec36), [§3.7](analysis.ipynb#sec37))*

- **44.5% of all campaign orders (55,487 of 124,767) contained a syrup bottle**; 62,836 bottles sold in 16 days.
- Flavour split: **Darko 49% · Syru 34% · Raptor 17%** — consistent enough that no flavour flopped, skewed enough to plan production toward Darko.
- **Every bottle was sold as part of a bundle** (100% `is_bundle`), with a **99.8% pods attach rate** and 81% sticker attach: the hero offer was clearly *bottle + 10er pods (+ sticker)*, and standalone bottle sales were effectively not a thing. Only 93 pod packs were sold without a bottle during the campaign.
- 88% of bottle orders took exactly one bottle (mean 1.13) — one bottle per household; multi-bottle gifting didn't materialize.
- **Bottle orders spend more:** AOV €91.7 vs €74.7 for campaign non-bottle orders (+23%).

### 3.2 Cross-purchase *(code [§3.8](analysis.ipynb#sec38))*

Among bottle orders (besides the bundled pods/sticker): **30% added a Shaker, 24% Merch, 16% an Energy tub**, 9% Iced Tea, 9% Hydration. Only 0.1% bought the bottle bundle alone. Syrup buyers are engaged multi-category customers — supports the "monetize the fans" read.

### 3.3 Delivery delay impact — the costliest lesson of the launch *(code [§3.9](analysis.ipynb#sec39))*

Bottle orders were dramatically slower than the rest of the shop:

| | Median delay | % > 7 days | % > 14 days |
|---|---|---|---|
| Campaign bottle orders | **8 days** | **52.1%** | **29.8%** |
| Campaign non-bottle orders | 4 days | 14.7% | 0.7% |

By order week, bottle orders placed in **weeks 18–19 (Apr 27 – May 10) waited a median 38–40 days** — the production gap hit customers who ordered late in the campaign; service recovered to ~5 days by week 20.

![Delay vs reorder](charts/delay_vs_reorder.png)

Reorder rate falls monotonically with the delay on the *first* bottle order: **14.5% → 11.0% → 9.7% → 5.4%**. Holding the ≤4-day rate as the counterfactual, the naive estimate is **~2,400 lost reorder customers (~4.5pp of cohort retention)**.
*Correction (see §5.6):* part of this gradient is calendar truncation — late-delivered customers had fewer days left to reorder before the data ends. A fixed 21-day-after-delivery window puts the **causal penalty at ~25% lower reorder likelihood, ≈1,000 lost reorders**. Still material, about half the naive read.

---

## 4. After the campaign

### 4.1 Retention & reorders *(code [§3.10](analysis.ipynb#sec310), [§3.12](analysis.ipynb#sec312))*

Of **53,738 identifiable campaign bottle buyers**:

- **10.1% (5,417) re-purchased syrup by May 31** (window: 4–6 weeks); median time to reorder **23 days** — consistent with a ~3-week consumption cycle for the bundled 10er pods.
- What they reordered: **3er pod bundles 3,989 · bottles 2,014 · 10er pods 1,924** (overlapping). The May 12 3er launch was well-timed against the consumption cycle and immediately became the main reorder vehicle (3,374 orders on day one).
- 3er bundle buyers since May 12: 10,236 orders, only **9.3% first-time customers**, and **42.4% verifiably owned a bottle already** — it is functioning as a retention product, as intended. (The other ~58% may have bought bottles before Apr 1, via another channel, or are gift buyers — worth checking, and one of my interviewer questions.)
- Reorder flavour ranking (Peach, Cola Ice Pop, Green Apple on top) is the first read on which refill flavours to scale.

### 4.2 Cannibalisation risk *(code [§3.11](analysis.ipynb#sec311))*

**No evidence of cannibalisation so far — if anything the opposite:**

- Energy absolute revenue per day *rose* pre → post (+36%).
- Cohort comparison: existing customers who bought a bottle grew their energy spend/day **+74%** pre→post, vs **+52%** for existing campaign customers who didn't buy a bottle.

**Interpretation with caveats:** syrup looks complementary (different consumption occasion) rather than substitutive. But the post window is short, seasonal, and boosted by other launches; the honest verdict is "no early warning signs", to be re-checked on a 90-day horizon — the real risk is *share-of-stomach* over months, not weeks.

### 4.3 What worked / what to fix / open questions

**Worked:** launch-day activation of the base (28% of campaign volume in one day); bundle architecture (100% pods attach = every bottle owner starts the refill loop); the May 12 3er bundle timed to the refill cycle; a durable post-campaign syrup baseline (~14% of revenue).

**Needs improvement:**
1. **Fulfilment readiness** — the production gap measurably burned early retention (~1,000 reorders on the corrected estimate, §5.6) and it was POZ1-specific (§5.4); late-campaign buyers had a first experience of waiting 5+ weeks.
2. **FR performance** — 55% uplift vs DE's 191%; diagnose localisation/creator mix before H2.
3. **New-customer angle** — syrup didn't acquire; consider a syrup-led acquisition offer now that the base is saturated with bottles.

**Couldn't answer with this data (and what I'd need):**
- **Campaign ROI** → media spend & channel attribution for the window.
- **True incrementality of launch-day demand** (pull-forward vs. net-new) → longer pre-period / prior-year baseline.
- **Retention beyond 4–6 weeks & pods consumption rate** → June+ orders; subscription data if any.
- **Margin** impact of the bundle discount → COGS/discount data (net revenue in the extract is unreliable — see data review).
- Whether **delivery-delay victims** should get a win-back voucher (I'd A/B it — the 15k customers who waited >14 days are a named, reachable audience).

---

## 5. Further analysis — beyond the brief's guiding questions

Seven analyses the case didn't ask for but that sharpen — and in one case honestly **revise** — the findings above.

### 5.1 Refunds & cancellations *(code [§6.1](analysis.ipynb#sec61))*
Refund incidence is tiny (~0.4% of campaign orders) and does **not** rise with delivery delay; cancellations are 2.7× higher for bottle orders (0.9% vs 0.3%) but still small. **The production gap's cost was retention, not immediate revenue give-back** — no product-quality red flag in refunds for a first-ever product.

### 5.2 The May 18 sampling giveaway barely converted *(code [§6.2](analysis.ipynb#sec62))*
The 5.4k new customers who received free samples on May 18 converted to a **paid order within 13 days at just 0.37%**, vs **4.8%** repeat-paid for organically acquired new customers in a matched window — ~13× worse. Short-term, the giveaway generated traffic, not customers. *(Caveat: 13 days is short; the leads may still monetise via CRM.)*

### 5.3 Campaign-acquired new customers are weaker, not stronger *(code [§6.3](analysis.ipynb#sec63))*
Fixed 21-day repeat window: campaign new customers **with** a bottle repeat at **4.5%**, vs 8.5% for campaign new customers without one and 9.3% for pre-period new customers. Even accounting for the ~23-day syrup refill cycle and the delivery delays, this further weakens syrup as an acquisition tool: it pulled few new customers (§2.2) and the ones it pulled repeat less — consistent with hype/gift buyers.

### 5.4 The production gap was a single-warehouse problem *(code [§6.4](analysis.ipynb#sec64))*
Bottle orders fulfilled from **POZ1 took a median 8 days (mean 13)**; **NOT1 shipped bottles in ~4 days throughout** — same as its normal service. The ops recommendation sharpens from "fix production planning" to a POZ1-specific stock-allocation fix (and consider cross-shipping from NOT1 during launches).

### 5.5 The launch re-activated dormant customers — and they stayed active *(code [§6.5](analysis.ipynb#sec65))*
Existing customers who bought a bottle nearly **doubled their total spend/day post-campaign (+88%)** while the matched control group *declined* (−5%). Notably, the bottle cohort was **less active pre-campaign** despite being larger — the launch pulled dormant fans back, and they kept ordering. *(Interpretation caveat: part of the lift is syrup refills themselves plus regression to the mean; a matched-activity control would isolate the pure habit effect.)*

### 5.6 Honest revision: the true delay penalty is smaller than the raw gradient *(code [§6.6](analysis.ipynb#sec66))*
The 14.5% → 5.4% gradient in §3.3 partly reflects **calendar truncation** (late-delivered customers had fewer days left to reorder before the data ends). Giving every customer the same **21-day clock starting at delivery** (deliveries ≤ May 10 only), the rates become **9.8% (0–4d) vs ~7.1–8.0% (delayed)** — a **~25% relative penalty**, or roughly **~1,000 lost reorders rather than the naive ~2,400**. The delay effect is real and material, but about half the naive estimate. TL;DR #4 and §3.3 should be read with this correction.

### 5.7 A refill-demand planning number *(code [§6.7](analysis.ipynb#sec67))*
From well-served owners' steady-state behaviour: **per 1,000 bottle owners, expect ~140 refill orders (~500 pod packs) per month**. First-month estimate from the launch cohort — refine with June+ data — but it's the number production planning lacked when the gap happened.

---

*Time spent: ~4.5 hours end-to-end (data audit, analysis, charts, write-up, further analyses), with the analysis fully scripted in Python/pandas for reproducibility.*
