# Further Analysis — Beyond the Brief's Guiding Questions

*Seven analyses the case didn't ask for but that sharpen — and in one case honestly **revise** — the findings of the [Q1 campaign analysis](q1-campaign-analysis.md). All numbers computed in analysis.ipynb §6.*

<a id="fa-1"></a>
## 1. Refunds & cancellations *(code: analysis.ipynb §6.1)*
Under the cancellation rule provided by Martijn (full refund), **bottle orders almost never cancelled: 0.02% vs 0.36%** for non-bottle orders — despite 5-week waits, customers kept their orders. Partial refunds are more common on bottle orders (1.3% vs 0.3%), consistent with goodwill/shipping credits on delayed orders, and refund incidence does **not** rise with delay (0.2–0.5% across delay buckets). **The production gap's cost was retention, not immediate revenue give-back.** *Note: this finding flipped direction once the refund-based cancellation rule replaced the earlier shipment-status proxy — the "cancelled" shipment rows on bottle orders were replacement re-shipments, not customer cancellations.*

<a id="fa-2"></a>
## 2. The May 18 sampling giveaway barely converted *(code: analysis.ipynb §6.2)*
The 5.4k new customers who received free samples on May 18 converted to a **paid order within 13 days at just 0.37%**, vs **4.8%** repeat-paid for organically acquired new customers in a matched window. The two cohorts sit at different funnel stages (a free-sample taker vs. someone who already paid), so the ~13× gap overstates the verdict — but near-zero immediate conversion still means the giveaway must be judged as **lead generation** (i.e., on the CRM follow-up to those 5.4k contacts), not as a sales driver. *(Caveat: 13 days is short.)*

<a id="fa-3"></a>
## 3. Campaign-acquired new customers are weaker, not stronger *(code: analysis.ipynb §6.3)*
Fixed 21-day repeat window: campaign new customers **with** a bottle repeat at **4.5%**, vs 8.5% for campaign new customers without one and 9.3% for pre-period new customers. Even accounting for the ~23-day syrup refill cycle and the delivery delays, this further weakens syrup as an acquisition tool: it pulled few new customers (Q1 briefing §2.2) and the ones it pulled repeat less — consistent with hype/gift buyers.

<a id="fa-4"></a>
## 4. The production gap was a single-warehouse problem *(code: analysis.ipynb §6.4)*
Bottle orders fulfilled from **POZ1 took a median 8 days (mean 13)**; **NOT1 shipped bottles in ~4 days throughout** — same as its normal service. The ops recommendation sharpens from "fix production planning" to a POZ1-specific stock-allocation fix. Before relying on cross-shipping from NOT1 in future launches, verify that its clean record reflects spare capacity rather than simply the UK's lower volume.

<a id="fa-5"></a>
## 5. The launch re-activated dormant customers — and they stayed active *(code: analysis.ipynb §6.5)*
Existing customers who bought a bottle nearly **doubled their total net spend/day post-campaign (+89%)** while the matched control group *declined* (−5%). Notably, the bottle cohort was **less active pre-campaign** despite being larger — the launch pulled dormant fans back, and they kept ordering. *(Interpretation caveat: part of the lift is syrup refills themselves plus regression to the mean; a matched-activity control would isolate the pure habit effect.)*

<a id="fa-6"></a>
## 6. Honest revision: the true delay penalty is smaller than the raw gradient *(code: analysis.ipynb §6.6)*
The 14.6% → 5.4% gradient in the Q1 briefing §3.3 partly reflects **calendar truncation** (late-delivered customers had fewer days left to reorder before the data ends). Giving every customer the same **21-day clock starting at delivery** (deliveries ≤ May 10 only), the rates become **9.9% (0–4d) vs ~7.1–8.0% (delayed)** — a **~24% relative penalty**, or roughly **~1,000 lost reorders rather than the naive ~2,400**. Two honesty notes: the corrected gradient is **not monotonic** — the >14d bucket (which, restricted to deliveries by May 10, contains the most enthusiastic early launch buyers) sits at 8.0%, *above* the 8–14d bucket — so composition effects remain, and the penalty is best read as **"roughly a fifth to a quarter", not a point estimate**. The delay effect is real and material, but about half the naive read and imprecisely sized. The Q1 briefing's TL;DR #4 and §3.3 are stated with this correction applied.

<a id="fa-7"></a>
## 7. A refill-demand planning number *(code: analysis.ipynb §6.7)*
From well-served owners' steady-state behaviour: **per 1,000 bottle owners, expect ~140 refill orders (~500 pod packs) per month**. First-month estimate from the launch cohort — refine with June+ data — but it's the number production planning lacked when the gap happened.
