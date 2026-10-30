# Amazon India Sales Analysis | Power BI

A four-page interactive Power BI dashboard built on an Amazon India sales export (~121k rows, 31 Mar – 29 Jun 2022). This is a practice project on a public dataset and is not affiliated with Amazon.

## Dashboard Preview

| Sales Analysis | Customer & Geographic Insights |
|---|---|
| [![Sales](images/01_sales_analysis.png)](images/01_sales_analysis.png) | [![Customer](images/02_customer_geographic_insights.png)](images/02_customer_geographic_insights.png) |

| Operations & Fulfillment | Data Quality Notes |
|---|---|
| [![Operations](images/03_operations_fulfillment.png)](images/03_operations_fulfillment.png) | [![Data Quality](images/04_data_quality_notes.png)](images/04_data_quality_notes.png) |

## Business Questions

- How much revenue is actually realized, and how is it trending month over month?
- Is the sales trend driven by order value or by order volume?
- Which categories, sizes and states drive revenue?
- How much revenue is lost to cancelled, returned or problem orders, and how does it differ by fulfilment method?
- How reliable is the data behind these numbers?

## Key Findings

- **Net Sales ≈ 69.7M** (about 88.6% of the 78.6M gross order value). Net = successfully shipped orders only.
- **Sales fell ~20% from April to June** (25.7M → 20.4M) while Average Order Value rose (686 → 727). The cause is order volume (37,396 → 28,123 orders); the cancellation rate was stable after April.
- **Two categories (Set and Kurta) generate about 77% of net sales.**
- **Amazon-fulfilled orders bring ~72% of net sales**, and Merchant-fulfilled orders are cancelled about twice as often (≈13.6% vs ≈6.7% of orders). Returns appear only on Merchant-fulfilled orders.
- **B2B is under 1% of net sales**; the business is almost entirely B2C.
- **Maharashtra and Karnataka account for ~31% of net sales**; the top 5 states for ~54%.
- **Lost and damaged shipments are negligible** (≈0.004% of orders).
- **2,343 non-cancelled rows (2.12%) have Amount = 0**; they are flagged and excluded from financial measures (see Data Quality).

> Figures are rounded and computed on the cleaned data; March contains only a few days (139 orders), so April growth vs March is not meaningful.

## KPI Definitions

| KPI | Definition | Notes |
|---|---|---|
| Net Sales | Sum of Amount where Status Group = Shipped Successfully and Is Data Issue = FALSE | Excludes cancelled, pending, returned, problem |
| Returned Sales | Sum of Amount where Status Group = Returned | Returned to seller / returning / rejected by buyer |
| Cancelled Sales | Sum of Amount where Status Group = Cancelled | Value of cancelled orders |
| Total Orders (Net) | Distinct Order ID of net sales | Order ID alone is not a unique row key |
| Average Order Value | Net Sales / Total Orders (Net) | |
| Cancellation Rate % | Distinct cancelled orders / distinct orders | |
| Return Rate % | Returned Sales / (Net Sales + Returned Sales) | |
| MoM Growth % | (Current − Previous month Net Sales) / Previous month | Uses the Date table + `DATEADD` |
| B2B / B2C Sales % | Net Sales by customer type / Net Sales | |
| Lost & Damage Rate % | Distinct orders Lost in Transit or Damaged / distinct orders | |
| Delivery Confirmation Rate % | Delivered to Buyer / Shipped Successfully (distinct orders) | |
| % Orders Awaiting Shipment | Orders with Courier Status = Unshipped / all orders | |

**Notes:** Percentages are rounded on the dashboard. All formulas are in [dax/measures.dax](dax/measures.dax).

## Data Preparation (Power Query)

- Kept 17 analytical columns; dropped `index`, `Sales Channel`, `ASIN`, `currency`.
- Set data types (B2B as true/false), applied `Trim` + `Clean` on all text columns.
- Standardized Courier Status casing with `Text.Proper` (`shipped` / `Shipped`).
- Replaced nulls in shipping columns with `Unknown`; handled errors (Date → null, Qty/Amount → 0, then removed remaining error rows).
- Removed fully duplicated rows after cleaning.
- Created a composite key **Order ID + SKU** (one order can contain several SKUs).
- Added **Status Group** and **Is Data Issue** columns; built a calendar `Date` table.

**Status mapping**

- **Shipped Successfully:** Shipped, Shipped - Picked Up, Shipped - Out for Delivery, Shipped - Delivered to Buyer
- **Returned:** Shipped - Returned to Seller, Returning to Seller, Rejected by Buyer
- **Cancelled:** Cancelled
- **Pending:** Pending, Pending - Waiting for Pick Up
- **Problem:** Lost in Transit, Damaged

Code: [powerquery/Amazon_Sale_Report.pq](powerquery/Amazon_Sale_Report.pq) · [powerquery/Date.pq](powerquery/Date.pq)

## Data Quality

- **Amount = 0 on non-cancelled orders:** 2,343 rows. Likely an export issue. Kept for order counts, excluded from financial measures through `Is Data Issue`.
- **Lowercase `shipped`:** 5,135 rows, all Cancelled + Merchant. Standardized in Power Query.
- **No Customer ID:** customer-behavior analysis (repeat purchases, CLV) is not possible.

Details: [docs/data_quality_notes.md](docs/data_quality_notes.md) · [docs/data_dictionary.md](docs/data_dictionary.md)

## Tools

Power BI Desktop · Power Query (M) · DAX

## Dataset

Public "Amazon Sale Report" dataset (Kaggle), used for practice. The raw file is not included; see [data/README.md](data/README.md).

## Files

- `powerbi/Amazon_Sales_Dashboard.pbix`: the dashboard
- `images/`: dashboard screenshots
- `powerquery/`, `dax/`: transformation and measure code
- `docs/`: data dictionary and data-quality notes

## Author

Muhammed Kamal
