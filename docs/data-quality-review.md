# Data Quality Review — HOLY Case Study Dataset

Systematic audit of `orders.csv`, `order_items.csv`, `products.csv` and `shipments.csv` (Apr 1 – May 31, 2026).
Every issue below was verified with code; numbers are exact counts from the files as delivered.
Issues are grouped by severity, and each one ends with the **question I would ask the interviewer**.

> **Reproducibility:** every check runs in **analysis.ipynb** (in the repository, rendered with outputs on GitHub) — the *(code: analysis.ipynb §…)* tag on each issue names the analysis.ipynb section that produces the exact counts quoted.

---

## 0. Update — additional info provided by Martijn

Three of the questions below have been answered; the analysis was re-run under the confirmed rules (every headline conclusion held, shifts ≤ 0.2pp):

1. **Revenue definitions (→ §2.4, resolved):** `gross_revenue` = SUM(item gross), item-level only. `net_revenue` = SUM(item gross) + `shipping_revenue` − `refunded_value` − total tax. This explains both the net > gross rows (shipping) and the negative-net rows (refunds); implied tax rates match VAT by market (code: analysis.ipynb §1.1). Per Martijn's follow-up guidance, the analysis uses **net revenue** as the money metric (it is what marketing steers on); gross appears only in this audit.
2. **Cancellations (→ §2.3, resolved):** a cancelled order is `refunded_value ≥ gross_revenue`. Applied with a `gross > 0` guard (zero-value sample orders trivially satisfy the rule); 838 orders excluded. One consequence: the further-analysis cancellation finding flipped — bottle orders almost never cancel (0.02%), confirming the "cancelled" shipment rows were replacement re-shipments. Note: applying the rule still leaves **61 orders** (of ~344k) with slightly negative net — refunds exceeding gross + shipping − tax — treated as partial-refund edge cases; worth confirming whether the intended threshold is gross alone or gross + shipping.
3. **Mystery SKUs (→ §2.7, resolved):** all `10-00-42-002x` SKUs are spare parts (replacements) for the syrup bottle, intentionally absent from the product master — excluded from the analysis entirely.

---

## 1. Schema mismatches vs. the case definition (ask first — cheap to clarify)

The files do not match the schemas described in the case brief:

| Case brief says | Dataset actually has |
|---|---|
| `orders.csv` has `channel` ("Sales channel") | No `channel` column at all |
| `orders.csv` has `is_cancelled` | No `is_cancelled` column (cancellation only inferable from `shipments.shipment_status = 'cancelled'`) |
| `orders.csv` has `total_net_revenue`, `total_gross_revenue` | Columns are named `net_revenue`, `gross_revenue`, plus undocumented `customer_id`, `is_first_paid_order`, `shipping_revenue`, `refunded_value` |
| File `base_product.csv` | File is named `products.csv` |
| `shipments.csv` has only `order_id`, `delivered_at` | Has 9 columns: `shipment_status`, `delivery_status`, `warehouse`, `shipped_at`, `days_to_delivery`, `days_to_ship`, `is_on_time`, … |
| Brief: "Syrup 3er Bundles (packs of **3 syrup bottles**…)" | `products.csv` names SKUs `11-00-43-0002-*` "Syrup **Pod** - 3er Bundle" — pods, not bottles |

> **Ask:** Is the delivered dataset a newer export than the brief? Which document is authoritative? And is the 3er bundle (`11-00-43-0002%`) three *bottles* or three *pod packs*? This changes the interpretation of the retention/reorder analysis (a pod reorder = consumption refill; a bottle reorder = gifting/expansion).

**How I handled it:** I treated the CSVs as authoritative and used the brief only for business context (campaign dates, SKU lists, the May 12 reorder rule).

---

## 2. High-impact issues (materially affect the analysis)

### 2.1 Duplicate orders — 500 duplicated `order_id`s in `orders.csv` *(code: analysis.ipynb §1.1)*
1,000 rows carry 500 duplicated IDs. 445 pairs are *not* byte-identical — they differ slightly in `shipping_revenue` (e.g. 0.00 vs 0.03/0.05). Looks like an export dedup failure.
**Handling:** kept the first occurrence. **Ask:** which extract is canonical, and is `shipping_revenue` reliable at all?

### 2.2 200 orphan order_items (`ORD-900001`–`ORD-900200`) *(code: analysis.ipynb §1.2)*
200 line items reference order IDs that don't exist in `orders.csv` — and the ID range (900k) is far outside the normal range (~345k). Looks like injected test data or a truncated orders extract.
**Handling:** excluded. **Ask:** are these test records, or is `orders.csv` missing rows?

### 2.3 No cancellation flag + re-shipment duplication in `shipments.csv` *(code: analysis.ipynb §1.4)*
- `shipments.csv` has 366,218 rows for ~345k orders: **21,555 orders have 2–4 shipment rows** (typical pattern: shipped → cancelled → shipped again weeks later). Naively joining shipments to orders double-counts and mixes up delivery dates.
- 1,545 orders have *only* cancelled/ignored shipment rows (my proxy for a cancelled order); 270 orders have no shipment row at all.

**Handling:** used the *first delivered* shipment per order for delay analysis.
**Ask:** ~~what is the correct definition of a cancelled order?~~ **Answered — `refunded_value ≥ gross_revenue` (see §0); now applied.** Still open: do multiple shipment rows mean split shipments, replacements, or subscription-like repeat deliveries? (Many orders show two *successful* deliveries ~3–4 weeks apart — the near-zero cancellation rate on bottle orders supports the replacement-shipment reading.)

### 2.4 Inconsistent net/gross revenue definitions in `orders.csv` *(code: analysis.ipynb §1.1, §1.2)*
- **37,098 orders (10.8%) have `net_revenue` > `gross_revenue`.** In these rows `net_revenue ≈ gross_revenue + shipping_revenue` exactly — i.e. net *includes* shipping while gross excludes it, unlike all other rows.
- 764 orders have negative `net_revenue` with positive gross.
- Net/gross ratios are otherwise VAT-coherent (UK ≈ 1/1.20; DE mixes 7% food / 19% standard; FR mixes 5.5% / 20%), which confirms the general logic is right but the shipping treatment is inconsistent.
- Sum of `order_items.net_revenue` fails to reconcile with `orders.net_revenue` for **12.3% of orders** (gross reconciles almost perfectly: 149 mismatches).

**Handling:** the audit-phase analysis ran on **gross revenue** (net looked inconsistent); once the definition was confirmed, the final analysis was switched to **net revenue** per Martijn's guidance. **Ask:** ~~what exactly is `net_revenue` net of?~~ **Answered — `net = SUM(item gross) + shipping − refunded − tax` (see §0); the "flips" were shipping and refunds, not an inconsistency.**

### 2.5 5,490 order-item rows with negative `net_revenue` but positive `gross_revenue` *(code: analysis.ipynb §1.2)*
Including rows like gross €9.03 / net −€16.84. Possibly discount over-allocation on bundle decomposition. **Handling:** gross used throughout. **Ask:** is bundle discount allocation known to over-assign discounts to low-price components?

### 2.6 `shipments.is_on_time` looks broken *(code: analysis.ipynb §1.4)*
344,406 rows are `False`, only 5,647 `True` — even 1–2-day deliveries are flagged not-on-time. Also `days_to_delivery` disagrees with `delivered_at − order_date` by >1 day in 145k rows (it appears to be measured from `shipped_at`, not order date). One row has `days_to_delivery = −1`.
**Handling:** ignored `is_on_time` and `days_to_delivery`; computed delay as `delivered_at − order_date` myself. **Ask:** what SLA does `is_on_time` encode, and from which anchor timestamp is `days_to_delivery` counted?

### 2.7 13,724 order-item rows reference SKUs missing from `products.csv` *(code: analysis.ipynb §1.2, §1.3)*
Top offenders:

| SKU | Rows | Note |
|---|---|---|
| `10-00-42-0020` | 7,069 | **In the Syrup Bottle SKU range (10-00-42)**, sold only from launch day (Apr 19), avg €4.13 — looks like a syrup accessory (extra cap/pump?) that belongs in the campaign scope but is absent from both the brief and the product master |
| `11-00-01-0043-01-1` | 2,225 | Malformed suffix (`-1`) of a valid Energy SKU — €83.6k gross affected |
| `11-00-10-0139` | 2,006 | Component of milkshake bundles, missing from master |
| `11-00-03-0016-02` / `11-00-02-0006-02` | 1,143 / 846 | Missing from master |
| `NOSYNC` | 144 | Placeholder; zero revenue; also flagged `is_bundle=True` with itself as component |
| `drako-1`, `raptor-1`, `syru-1` | 17 | Lowercase free-text SKUs that look like manually keyed syrup bottles ("drako" vs product name "Darko") |

**Handling:** initially kept; now **excluded** per Martijn. **Ask:** ~~what is `10-00-42-0020`?~~ **Answered — `10-00-42-002x` are syrup-bottle spare parts, out of scope (see §0).** Still open: should the free-text/malformed SKUs (`drako-1`, `11-00-01-0043-01-1`, …) be mapped back to real products?

### 2.8 Customer identity problems (breaks retention analysis at the margin) *(code: analysis.ipynb §1.1)*
- 3,450 orders have **no `customer_id`** — these can never appear in reorder/retention metrics.
- **1,583 customers have more than one order flagged `is_first_order = TRUE`.**
- 10,137 orders are `is_first_order=TRUE` but `is_first_paid_order=FALSE` (plausible if the first order was free), yet 1,211 are the reverse — `is_first_order=FALSE` but `is_first_paid_order=TRUE` *and* that combination is hard to explain if flags are computed consistently.

**Ask:** how is customer identity resolved (email? account?), and are the first-order flags computed per shop or globally? Guest checkout = null customer_id?

---

## 3. Anomalies that look like business events, not errors (need context) *(code: analysis.ipynb §1.5)*

These change how the campaign windows should be read — I'd validate my reading with the team:

1. **May 18 spike:** 9,965 orders, but 55.7% have **zero gross revenue**, AOV €31, **72.3% first-time customers**, dominated by free Logo Shakers and Mixed Sachet Boxes. Looks like a **lead-gen / sampling giveaway**. It also coincides with a large wave of *re-shipments*. It inflates "orders" and depresses AOV and the new-customer quality picture for the post-period. → *Confirmed promo? Should zero-revenue orders be excluded from KPIs?*
2. **May 27 spike:** 12,159 orders, AOV €104 — driven by a **"Summer Cocktail 2026" launch** (ice cube trays, beach towels, cocktail-flavour tubs). A separate campaign that contaminates any "post-syrup-campaign baseline". → *Confirm so post-period comparisons can exclude it.*
3. **14,082 zero-gross orders overall** (only 87 of them cancelled) — freebies, replacements, or 100% discount codes?
4. **Pre-launch syrup sales:** 2 bottle orders and 2 pod orders dated Apr 16–18, before the Apr 19 launch (test orders? employees? early unlock?). Similarly, **155 zero-revenue 3er-bundle rows appear from Apr 19 onward**, before the official May 12 launch — they look like seeded samples/influencer sends. The brief's "count 3er as reorders" rule is unaffected (real sales clearly start May 12: 3,374 orders that day), but the early zero-value rows should be excluded from revenue.
5. **UK store is much smaller** (36k orders vs DE 177k / FR 132k) — sampled differently, or genuinely small market? Affects per-market conclusions.
6. **50,560 deliveries dated after May 31** (up to Jun 21) — expected, since late-May orders deliver in June; just note the shipment file has a longer horizon than the order file.

---

## 4. Summary — top questions for the interviewer

1. ✅ *Answered (§0).* ~~What is `net_revenue` net of, and why does its definition flip for ~11% of orders?~~
2. ✅ *Answered (§0):* cancelled = `refunded_value ≥ gross_revenue`.
3. Do multiple shipment rows per order represent **split shipments or replacements** (esp. the Apr→May re-ship wave around the syrup production gap)?
4. What are the **May 18** (zero-revenue sampling?) and **May 27** (Summer Cocktail launch) events, and should they be excluded from post-campaign baselines?
5. ✅ *Answered (§0):* `10-00-42-002x` are syrup-bottle spare parts; excluded.
6. Is the 3er bundle **pods or bottles**? (Brief and product master disagree.)
7. How is `customer_id` assigned (guest checkout → null?) and why do 1,583 customers have two "first" orders?
8. Which timestamp anchors `days_to_delivery`, and what SLA defines `is_on_time` (currently 98% False)?
9. Is the UK store's small size real or a sampling artifact?
10. Was there any **paid media / discount** behind the Apr 19 launch spike (34.5k orders, 6× normal)? Without spend data, campaign ROI can't be computed — only revenue uplift.

---

## 5. Cleaning rules applied in my analysis *(code: analysis.ipynb §2)*

| Rule | Rows affected |
|---|---|
| Dedupe `orders` on `order_id` (keep first) | −500 |
| Drop order_items with order IDs not in `orders` | −200 |
| Exclude cancelled orders — rule provided by Martijn: `refunded_value ≥ gross_revenue` (guarded to gross > 0) | −838 |
| Drop `10-00-42-002x` spare-part line items (per Martijn: out of scope) | −7,296 item rows |
| Use **net revenue** for all monetary metrics (additional info provided by Martijn; gross used during the audit phase) | — |
| Compute delivery delay as `delivered_at − order_date` (first delivered shipment) | — |
| Retention restricted to orders with a `customer_id` | 54,127 of 55,982 bottle buyers identifiable |
