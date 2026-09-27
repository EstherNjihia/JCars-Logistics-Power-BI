# JCars-Logistics-Power-BI
This project transforms a vehicle sales dataset from Kenya into a Power BI report covering sales, deliveries, vehicle performance, customers, and logistics costs. It combines data cleaning, a star-schema model, and documented business rules so that the dashboard’s results can be interpreted accurately.

## Project overview

The source is an operational flat file with **one vehicle order line per row**. An order line may contain multiple vehicles, so the report measures **order lines** and **vehicle units** separately.

The dataset contains inconsistent IDs, dates, categories, locations, currencies, and numeric values. Standardised `JC-####` IDs identify records within this project; they are not confirmed permanent IDs from the source system.

## Repository structure

```text
JCars-Logistics-Power-BI/
├── README.md
├── Jcars_model.pbix
└── images/
    ├── Executive_Summary.png
    ├── Vehicle_Performance.png
    ├── Salesrep_Performance.png
    ├── Customers_Sales_Channels.png
    ├── Operations_DataQuality.png
    └── Logistics_Cost.png
```

## Data preparation

Power Query transformations preserve source values where needed for traceability, standardise fields used for analysis, and flag unresolved exceptions. Missing information is not filled with unsupported estimates.

| Area | Treatment |
| --- | --- |
| Order IDs | Standardised for analysis; original values retained |
| Text and categories | Cleaned spacing and case; documented aliases mapped |
| Locations | Customer location kept separate from selling branch |
| Dates | Known text and spreadsheet serial dates parsed; unresolved dates left blank |
| Date sequences | Delivery-before-order records flagged for review |
| Vehicle attributes | Make, model, fuel type, transmission, colour, and vehicle type standardised |
| Numeric fields | Unambiguous text values converted to numbers; invalid values flagged |
| Discounts | “No Discount” mapped to zero; valid percentages standardised |
| Currency | Currency identified before conversion using a fixed reference-rate convention |
| Missing monetary values | Left missing rather than replaced with averages or zero |
| Return status | Missing values shown as “Not Provided” |

Currency markers that could not be identified directly from the source were handled using documented project assumptions. 

## Data model and measures

The report uses a star schema centred on `Fact_Sales`, with date, branch, sales representative, car model, and customer-location dimensions. Relationships run from the dimensions to the fact table.

The active reporting date is the cleaned **order date**. Delivery-date analysis requires explicit relationship handling.

| Measure | Business rule |
| --- | --- |
| Orders Recorded | Distinct standardised order IDs |
| Gross Cars Delivered | Positive vehicle units on qualifying delivered lines, excluding cancelled or refunded payments |
| Delivered Order Lines | Lines meeting the qualifying delivery rule |
| Calculated Vehicle Revenue | Qualifying units × cleaned selling price × valid discount adjustment; excludes delivery fees |
| Vehicle Gross Profit | Calculated vehicle revenue less vehicle cost |
| Recorded Revenue | Currency-converted source revenue, retained for reconciliation |
| Delivered Net Delivery Fees | Delivery fees less logistics costs where both values are available |

Calculated revenue and profit may use different sets of complete records. Interpret each measure according to its own business rule rather than comparing headline cards without checking their filters.

## Business findings

- **Vehicle volume differs from order volume.** A line can contain multiple vehicles, so a make’s delivered-unit total is not its order count.
- **Toyota leads in delivered vehicle volume.** Kakamega Yard leads the displayed selling-branch comparison. Customer location is analysed separately from the selling branch.
- **Volkswagen profitability is sensitive to an exception.** Order `JC-0095` has an unusually high recorded selling price and materially affects the result. It remains flagged for invoice and cost verification.
- **Logistics costs exceed delivery fees** among qualifying delivered records with both amounts available. This finding does not cover records missing either value.
- **Date quality limits delivery-cycle analysis.** Missing or unresolved dates prevent some comparisons, while reversed order-to-delivery sequences remain flagged for investigation.

## Dashboard pages

| Page | Purpose |
| --- | --- |
| **Executive Summary** | Core measures, trends, delivered units by make and branch |
| **Vehicle Performance** | Make, model, calculated revenue, and vehicle gross profit |
| **Sales Rep Performance** | Delivered units, revenue, and profit by representative |
| **Customers & Sales Channels** | Customer segments and lead sources |
| **Operations & Data Quality** | Payment, delivery, return, and date-sequence analysis |
| **Logistics Costs** | Delivery fees, logistics costs, and branch-level net fees |
| **Sales Rep Order Details** | Hidden drill-through page for individual order lines |

## Interpretation limits

- The source has no persistent customer ID, so the report does not claim unique customers, repeat purchases, retention, or customer lifetime value.
- The meaning of `Returned = Yes` is not confirmed. The field is not used as a completed-return rate.
- Missing dates and monetary values limit some analyses.
- Assumed currency classifications require confirmation against source records.
- Generated dimension keys are suitable for this extract but may change if new dimension members are added.

## Reproduce the report

1. Open `Jcars_model.pbix` in Power BI Desktop.
2. Update the Power Query source step to the authorised local CSV.
3. Review the mapping queries and `ExchangeRate` reference table.
4. Refresh the model and check the report measures and drill-through page.

**Tools:** Power BI Desktop · Power Query · DAX · CSV · Star-schema modelling

## Related article
[Before the Dashboard: Cleaning and Modelling Messy Logistics Data in Power BI](https://dev.to/esther_njihia/before-the-dashboard-cleaning-and-modelling-messy-logistics-data-in-power-bi-4lcf)


