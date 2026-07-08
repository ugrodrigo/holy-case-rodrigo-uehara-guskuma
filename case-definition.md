# Case Study - Marketing analyst

# Question 1 - Campaign analysis

## 1. Campaign Brief

HOLY's **5th BURRsday - Syrup Campaign** was a major brand campaign celebrating the company's 5th anniversary. The hero launch was the **Syrup Bottle:** a brand-new product format entering the market for the first time.

| Detail | Value |
| --- | --- |
| Campaign period | April 19 – May 4, 2026 (16 days) |
| Markets | DE, FR, UK |
| Channel | Shopify  |

### Hero launch: Syrup Bottles & Syrup Pods 10er pack

The Syrup Bottle and Syrup Pods launched as part of the campaign:

| Product | component SKU |
| --- | --- |
| Syrup Bottle - Darko | `10-00-42-0001-01` |
| Syrup Bottle - Syru | `10-00-42-0001-02` |
| Syrup Bottle - Raptor | `10-00-42-0001-03` |
| Syrup Pods - 10er pack | `11-00-43-0001` |

Bottles were sold individually and as part of bundles. In the data, bundles are decomposed into their component SKUs.

### Other campaign products

The Syrup Campaign promoted other HOLY products alongside the Syrup launch.

| Product | component SKU |
| --- | --- |
| HOLY Energy® BURRsday Bunny | `11-00-01-0039-01` |
|  Bunny Animal Shaker | `11-00-10-0095-35` |
| HOLY Energy® Energy Eel | `11-00-01-0024-01` |
|  Eel Animal Shaker | `11-00-10-0095-20` |
| HOLY Energy® Cotton Candy Caterpillar | `11-00-01-0045-01` |
|  Caterpillar Animal Shaker | `11-00-10-0095-45` |
| Stickerpack | `11-00-13-0017` |
| Key Caps Set | `11-00-11-0200` |
|  Syrup T-Shirt | `11-00-11-0199` |

### Important context

- The syrup bottle was a **first-ever product launch,** there are no prior sales to compare against.
- Some syrup bottle orders experienced **delivery delays due to a production gap**.
- On **May 12, 2026**, HOLY launched **Syrup 3er Bundles** (packs of 3 syrup bottles in a single flavour, SKU pattern: `11-00-43-0002%`). Orders of this product should count as **reorders** for syrup retention analysis.

### About the dataset

This dataset is based on realistic e-commerce data but has been modified for confidentiality. Product names, SKUs, and categories are real. Dates and behavioural patterns reflect real dynamics. Absolute numbers (revenue, order counts) should not be taken at face value.

Focus your analysis on patterns, ratios, and relative comparisons rather than quoting absolute numbers as fact.

---

## 2. Dataset

case-study-dataset.zip

### `orders.csv` - Orders data sample (April 1 – May 31, 2026)

A sample of orders placed by customers in DE, UK, and FR stores.

### `order_items.csv`  - Order items data sample (April 1 – May 31, 2026)

Line items detail for orders in dataset A

### `base_product.csv` - Product data

Line items detail for orders in dataset A

### `shipments.csv` - Shipment data

Delivery information for campaign orders, so you can analyse fulfilment timing and identify delayed shipments.

---

## 3. Table Schemas

### `orders.csv`

One row per order.

| Field | Description |
| --- | --- |
| `order_id` | Unique order identifier |
| `order_date` | Date of the order |
| `shop` | Market code: DE, FR, UK |
| `channel` | Sales channel |
| `is_first_order` | Boolean |
| `is_cancelled` | Boolean |
| `total_net_revenue` | Net revenue for the full order |
| `total_gross_revenue` | Gross revenue for the full order |

### `order_items.csv`

One row per product component per order. Bundles are broken down into their individual components.

| Field | Description |
| --- | --- |
| `order_id` | Links to `orders` |
| `ordered_sku` | The SKU the customer actually purchased |
| `component_sku` | The individual product SKU after bundle decomposition |
| `is_bundle` | Boolean (TRUE if this product comes from a bundle) |
| `quantity` | Units of this component |
| `gross_revenue` | Gross revenue attributed to this component |
| `net_revenue` | Net revenue attributed to this component |

### `base_product.csv`

| Field | Description |
| --- | --- |
| `sku` | Product SKU |
| `category` | Product category |
| `product_name` | Product name |

### `shipments.csv`

| Field | Description |
| --- | --- |
| `order_id` | Links to `orders` |
| `delivered_at` | Timestamp when the order was delivered to the customer |

---

## 4. Your Task

Analyse the **5th BURRsday - Syrup Campaign** with a focus on the new products launch. Present your findings as if briefing the Head of Marketing.

You can work with the data using any tool you want.

Structure your analysis however you think is most effective, you can use the questions below for guidance, but feel free to explore and present your insights beyond those questions.

---

## 5. Guiding Questions

### Setting Expectations

1. **What KPIs would you track** to evaluate a product launch campaign like this? List them and explain why each matters.
2. **How would you define success** for the syrup bottle launch? What would "good" look like at the end of the campaign vs 30 days later?

### Campaign Analysis

1. **Baseline & uplift**
2. **Existing vs New Customers dynamics**
3. **Market breakdown**
4. **Daily trends**
5. **Category mix**

### Products Deep Dive

1. **Adoption**
2. **Buying behaviour**
3. **Cross-purchase**
4. **Delivery delay impact**

### After the Campaign: Retention & Strategic Thinking

1. **New product reorders after campaign**
2. **New product cannibalisation risk**
3. **Recommendations:** What worked, what needs improvement, what questions were you not able to answers yet? what data would you need to decide?

---

## 6. Deliverable

- A structured write-up your choice
- Key charts and tables supporting your analysis
- Clear distinction between what the data shows vs your interpretation/hypothesis
- Time spent: please note roughly how long it took you

---

# Question 2 - H2 Sales budgets

**Background**

During H1 2026 we have been working hard to set up our Marketing Mix Model (MMM). Before this, we relied solely on attribution to steer our German marketing spend.

We are now planning our H2 budget and deciding how to allocate spend across channels. For this exercise, assume a total H2 marketing spend of **€35M**, of which **€4M is ring-fenced for brand**. Within the brand budget, **€800K is already locked in Q3 for TV**.

The attached document contains:

- **Section A** — MMM v5.3.1 results: L1 channel efficiency (iROAS, mROI), L2 sub-channel breakdowns for Paid Social, Influencer and Brand, and the H2 budget recommendation built by the MMM team.
- **Section B** — Attribution data for the DE market across March, April and May 2026, including attributed new customers (NC), CAC and 6-month CLV per channel.

1782235684569_holy_cmo_memo_mmm_h2_2026.pdf

Use both data sources as your primary inputs. Be ready to explain your reasoning and answer follow-up questions.

**Task 1 — Budget Allocation**

Prepare a rough H2 budget with spend allocations per channel, based on the MMM output.

Apply the following constraints:

- Total H2 spend: €35M
- Brand envelope: €4M (fixed)
- Within brand: €800K locked for TV in Q3
- Remaining €31M is fully flexible across performance channels

Show your channel-by-channel breakdown and the logic behind each allocation decision.

**Task 2 — Testing Roadmap**

Propose a testing roadmap that identifies which MMM results you want to validate through incrementality tests.

Your roadmap should:

- Prioritise which findings to test first and explain why
- Define the hypothesis, measurement approach and decision trigger for each test
- Be aligned with your budget proposal from Task 1 — tests should directly inform future allocation decisions
- Account for sequencing and potential interference between experiments

**Task 3 — Strategic Thinking**

Provide a structured reflection on the following:

- **Assumptions** — what assumptions did you have to make to complete Tasks 1 and 2, and how confident are you in each?
- **Unclear or unusual data points** — flag anything in the MMM or attribution data that struck you as odd, inconsistent or hard to interpret, and explain how you handled it
- **Missing business context** — what additional information would you need to make better decisions? Think about data, market context, operational constraints, or anything else that would materially change your recommendations