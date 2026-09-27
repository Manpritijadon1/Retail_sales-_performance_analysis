# Retail Sales Performance & Business Analytics

**Author:** Manpreet Jadon  
**Project type:** Data Analytics  
**Study period:** 2022–2025

## Project Overview

This project analyzes the supplied retail sales transactions covering 2022 through 2025. A Jupyter Notebook profiles the data, assesses quality, prepares a separate analysis copy, and explores recorded sales and profit across products, customers, sales channels, geography, and time.

The purpose is to turn transaction-level data into transparent, reproducible business summaries and visualizations, while documenting where missing data, duplicate records, inconsistent labels, and unusual values affect interpretation. Financial amounts are presented in the source's units: the data dictionary does not specify a currency.

## Business Problem

Retail decision-makers need a dependable view of how recorded sales and profit vary across products, customers, channels, locations, and time. Duplicate transactions, incomplete fields, and inconsistent or unusual values can distort those comparisons. This project provides a documented analysis workflow and makes its metric definitions and data-quality decisions visible, rather than silently imputing or changing the source data.

## Objectives

- Review the actual transaction file and its field dictionary.
- Measure missingness, exact duplicates, categorical label inconsistencies, and selected numeric range anomalies.
- Preserve the original data and prepare a separate, conservatively cleaned analysis copy.
- Describe recorded sales, cost, profit, and margin, with status-level context.
- Compare products, customers, channels, and geographic fields where available.
- Analyze annual, quarterly, and monthly sales patterns using the transaction date.
- Explore outliers and numeric correlations without treating them as causal evidence.
- Present evidence-based insights and practical recommendations with explicit limitations.

## Dataset

| Item | Details |
|---|---|
| Dataset | Sales Transactions 2022–2025 |
| Source | Files supplied with the project; no further source or collection method is documented. |
| Transaction file | `Sales_transactions_2022_2025.csv` |
| Data dictionary | `sales_data_dictionary.csv` |
| Period in `Order_Date` | 2022-01-01 through 2025-12-31 |
| Source size | 18,045 rows × 36 columns |
| Exact duplicate rows | 45 extra exact copies, removed from the analysis copy |
| Analysis size after exact duplicate removal | 18,000 rows × 36 source fields, plus derived analysis features |

The transaction file contains the following **actual fields**:

```text
Transaction_ID, Order_ID, Customer_ID, Customer_Name, Customer_Age,
Customer_Gender, Customer_Segment, Order_Date, Order_Time, Sales_Channel,
Store_ID, Store_Name, Country, Region, City, Product_ID, Product_Name,
Product_Category, Product_Subcategory, Quantity, Unit_Price,
Discount_Percentage, Sales_Amount, Cost_Amount, Profit, Payment_Method,
Order_Status, Shipping_Method, Delivery_Days, Return_Flag, Return_Reason,
Sales_Representative, Promotion_Code, Customer_Rating, Inventory_Level,
Order_Year
```

### Important fields

| Analysis area | Fields used |
|---|---|
| Transaction, order, customer keys | `Transaction_ID`, `Order_ID`, `Customer_ID` |
| Dates and time | `Order_Date`, `Order_Time`, `Order_Year` |
| Sales and profitability | `Quantity`, `Unit_Price`, `Discount_Percentage`, `Sales_Amount`, `Cost_Amount`, `Profit` |
| Product | `Product_ID`, `Product_Name`, `Product_Category`, `Product_Subcategory` |
| Customer | `Customer_Age`, `Customer_Gender`, `Customer_Segment` |
| Channel and geography | `Sales_Channel`, `Country`, `Region`, `City`, `Store_ID`, `Store_Name` |
| Operations and feedback | `Order_Status`, `Return_Flag`, `Return_Reason`, `Shipping_Method`, `Delivery_Days`, `Customer_Rating` |

The accompanying dictionary documents all 36 source fields with a description, declared data type, example value, and business meaning. It describes `Transaction_ID` as a unique transaction key; `Order_ID` groups order lines; `Customer_ID` supports repeat-customer analysis; and `Order_Year` as a convenience field that can be recalculated from `Order_Date`. The notebook checks that the dictionary field list matches the transaction headers.

## Data Quality & Preprocessing

The notebook retains `raw_df` as the original loaded data and applies changes only to a working dataframe, `df`. It does not overwrite the CSV.

| Check or preparation | Observed / performed |
|---|---|
| Missing values | Measured by field before cleaning. Blank text is converted to missing in the analysis copy; no missing values are imputed and rows are not dropped merely for missingness. |
| Most-missing fields in source | `Return_Reason`: 16,168; `Promotion_Code`: 9,737; `Customer_Rating`: 3,435. |
| Other fields with missing values | `Customer_Age`: 218; `Inventory_Level`: 198; `Customer_Gender`: 162; `Delivery_Days`: 162; `Payment_Method`: 147; `Product_Name`: 144; `Customer_Name`: 108. |
| Duplicate records | 45 exact duplicate rows are removed from the analysis copy. Repeated transaction IDs in the raw data are exact copies, not differing record versions. |
| Text consistency | Leading/trailing whitespace is trimmed. Case-only categorical variants are mapped to the most frequently occurring spelling. Semantic aliases are not automatically merged. |
| Data types | Numeric fields are parsed as numeric; `Order_Date` is parsed as a date. `Order_Time` is parsed for hour extraction. |
| Date features | The notebook derives year, month, month name, quarter, and year-month from `Order_Date`, and derives `Order_Hour` from `Order_Time`. Year-based analysis uses the parsed date. |
| Invalid or unusual values | Values are flagged, not silently corrected or discarded. The source contains one age of 112, three negative quantities (-1, -2, -3), and one discount value of 110%. No customer ratings outside 1–5 were found. |
| Financial values | `Sales_Amount`, `Cost_Amount`, and `Profit` are analyzed as recorded; the notebook does not recompute or overwrite them. |
| Outliers | IQR bounds are used to screen selected measures. Flagged values remain in the analysis. |

Examples of inconsistent labels in the source include case variants such as `electronics` / `Electronics` and spelling or alias variants such as `DebitCard` / `Debit Card`, `NRW` / `North Rhine-Westphalia`, and `British columbia` / `British Columbia`. Case-only normalization does not resolve semantic aliases.

## Exploratory Data Analysis

The notebook performs the following analyses using the cleaned analysis copy:

- **Overall sales performance:** sums recorded `Sales_Amount`, `Cost_Amount`, and `Profit`; calculates overall profit-to-sales margin.
- **Order and customer activity:** counts distinct orders and customer IDs and calculates average recorded sales per order by grouping sales lines on `Order_ID`.
- **Order status:** compares transaction rows, recorded sales, and recorded profit by `Order_Status`.
- **Product performance:** summarizes sales, profit, transaction rows, units, and margin for product categories; ranks products by recorded sales.
- **Customer analysis:** ranks customer IDs by recorded sales and profit, and summarizes sales by customer segment and gender.
- **Geographic analysis:** compares sales and profit by country, region, and city. Geographic aliases are not silently reconciled.
- **Annual trends:** calculates calendar-year recorded sales, profit, distinct orders, profit margin, and year-over-year sales change from `Order_Date`.
- **Monthly and quarterly trends:** plots monthly sales over time and provides quarterly totals.
- **KPIs:** reports recorded sales, profit, cost, profit margin, distinct orders/customers, average sales per order, channel sales share, and transaction rows marked as returned.
- **Return measures:** reports the share of transaction rows with `Return_Flag = Yes` separately from rows with `Order_Status = Returned`.
- **Outlier analysis:** applies the IQR screening rule to available sales, profit, quantity, and customer-age fields, and reports selected range flags.
- **Correlation analysis:** calculates and plots Pearson correlations across available numeric measures. Correlation is descriptive and is not interpreted as causation.

### Sales results from the notebook

These totals include every row remaining in the analysis copy, regardless of order status. The dictionary does not specify a currency or the accounting treatment for cancelled, returned, shipped, or in-progress orders.

| Measure | Result |
|---|---:|
| Recorded sales | 8,234,090.60 |
| Recorded cost | 6,590,388.19 |
| Recorded profit | 1,645,439.40 |
| Profit ÷ sales | 19.98% |
| Distinct orders | 14,541 |
| Distinct customer IDs | 4,882 |
| Average recorded sales per order | 566.27 |
| Rows flagged `Return_Flag = Yes` | 1,873 of 18,000 (10.41%) |

## Visualizations

The notebook creates the following business-focused charts:

- Horizontal bar chart of missing-value percentage by field.
- Annual recorded-sales bar chart.
- Monthly recorded-sales time-series line chart.
- Box plots for selected numeric measures, with IQR-flagged observations retained.
- Annotated correlation heatmap for selected numeric fields.
- Horizontal bar charts comparing recorded sales by product category, channel, country, and customer segment.
- Category sales by calendar year line chart.

The plots are generated when the notebook runs; the notebook is saved without precomputed cell outputs.

## Key Business Insights

The following are descriptive findings calculated from the cleaned analysis copy:

- Recorded sales total **8,234,090.60** and recorded profit totals **1,645,439.40**; currency is unspecified.
- Annual sales were **2,008,643.54** in 2022, **2,021,352.88** in 2023, **1,905,207.68** in 2024, and **2,298,886.50** in 2025.
- Year-over-year recorded sales changed by **+0.63%** in 2023, **-5.75%** in 2024, and **+20.66%** in 2025.
- **Electronics** is the leading product category by sales, at **4,922,081.42**.
- **Online** is the leading sales channel, at **3,112,092.21**.
- The **United States** is the leading country by recorded sales, at **3,503,885.80**.
- **Consumer** is the top customer segment by recorded sales, at **3,666,739.39**.
- Q4 has the largest cumulative calendar-quarter sales total across the four years, **2,697,536.77**. A cumulative total alone does not prove a recurring seasonal effect.
- **10.41%** of analysis transaction rows are marked `Return_Flag = Yes`. This is a transaction-row rate, not an order-level or value-weighted return rate.

## Business Recommendations

Recommendations are based on the measured results and documented quality flags; they do not assert causes that the dataset cannot establish.

- Investigate the upstream cause of the 45 exact duplicate rows and enforce transaction-key deduplication during ingestion.
- Confirm with data owners whether negative quantities represent returns or adjustments, and validate the age value of 112 and discount value of 110 before applying a reporting policy.
- Agree a finance-approved rule for including each `Order_Status` and treatment of returns before publishing accounting revenue KPIs.
- Standardize semantic aliases for geography and payment methods in a governed source-system lookup so regional and channel reporting is consistent.
- Decompose the 2024 sales decline and 2025 rebound by product, channel, order status, and volume/price mix before attributing causes.
- Track row-, order-, and value-based return measures separately, and reconcile `Return_Flag` with `Order_Status`.
- Document the currency and financial conventions for `Sales_Amount`, `Cost_Amount`, and `Profit`.

## Technologies Used

- **Python** — notebook execution and analysis workflow.
- **Pandas** — data loading, cleaning, grouping, and time-series summaries.
- **NumPy** — numerical operations.
- **Matplotlib** — charts.
- **Seaborn** — statistical visualization styling and plots.
- **Jupyter Notebook** — reproducible analysis and narrative.
- **IPython** — notebook display of formatted findings.

The exact pinned project dependencies are listed in [`requirements.txt`](requirements.txt). The analysis does not use a separate statistical modeling package.

## Project Workflow

**Data Collection**  
→ **Data Understanding**  
→ **Data Cleaning**  
→ **Data Preparation**  
→ **Exploratory Data Analysis**  
→ **Visualization**  
→ **Business Insights**  
→ **Recommendations**

## Project Structure

```text
.
├── ManpreetJadon_RetailSalesPerformanceAnalytics.ipynb
├── ManpreetJadon_RetailSalesPerformanceAnalytics_ProjectReport.docx
├── README.md
├── requirements.txt
├── Sales_transactions_2022_2025.csv
└── sales_data_dictionary.csv
```

Both dataset files are present alongside the notebook in this project workspace. If publishing the datasets is not appropriate for your repository, distribute them separately and follow the placement instructions below.

## How to Run

1. Clone the repository:

   ```bash
   git clone <repository-url>
   cd <repository-folder>
   ```

   Replace the placeholders with the repository URL and folder name.

2. (Recommended) Create and activate a virtual environment using Python 3.10 or later:

   ```bash
   python -m venv .venv
   ```

   Windows PowerShell:

   ```powershell
   .\.venv\Scripts\Activate.ps1
   ```

   macOS/Linux:

   ```bash
   source .venv/bin/activate
   ```

3. Install project requirements:

   ```bash
   python -m pip install -r requirements.txt
   ```

4. Ensure these files are in the notebook's working directory (the project root):

   ```text
   Sales_transactions_2022_2025.csv
   sales_data_dictionary.csv
   ```

   The notebook expects these exact filenames and raises a `FileNotFoundError` if either is absent.

5. Open the notebook:

   ```bash
   jupyter notebook ManpreetJadon_RetailSalesPerformanceAnalytics.ipynb
   ```

   Or use JupyterLab / VS Code, select the environment with the installed requirements, and open the notebook.

6. Run all cells from top to bottom. The notebook reads both CSV files from the current working directory, creates an in-memory cleaned analysis copy, and displays summaries and visualizations. The source CSV files are not modified.

## Key Takeaways

- The project provides a complete, reproducible workflow for reviewing and analyzing the supplied 2022–2025 retail transaction data.
- It preserves the source data, removes only exact duplicate records from the analysis copy, and makes missingness and unusual values visible.
- Recorded sales, profit, product/channel/geographic rankings, customer segments, and date trends are calculated directly from the supplied dataset.
- Results require context: the currency and revenue treatment by order status are undocumented, and source-quality flags should be reviewed before operational or financial decisions.

## Limitations

- The files document no original collection source beyond being supplied with the project, and do not specify currency, fiscal calendar, or financial recognition rules.
- Top-line sales and profit include all retained rows regardless of `Order_Status`; they are recorded-data totals, not audited net revenue.
- Missing values are not imputed. Demographic, service, rating, promotion, and return-reason analysis may have fewer usable records.
- Case-only label normalization does not reconcile semantic aliases such as `NRW` with `North Rhine-Westphalia`.
- IQR outlier flags are screening indicators; they do not prove errors. Negative quantities may have a valid returns/adjustments meaning that is not documented.
- Calendar-year and cumulative-quarter comparisons are descriptive, not inflation-adjusted, causal, or like-for-like analyses.
- The notebook has no saved execution outputs; summaries and charts are produced when its cells are run.

## Future Improvements

- Confirm currency, order-status accounting rules, and return-adjustment treatment with the data owner.
- Add automated validation rules for transaction-key uniqueness, date/year consistency, allowed discount and rating ranges, and the agreed quantity policy.
- Maintain source-system reference tables for geographic and payment aliases.
- Add order-level and value-weighted return measures after definitions are approved.
- Extend customer analysis with cohort and retention measures, and extend time-series work with documented like-for-like comparisons and validated forecasting methods.
- Re-run notebook quality checks automatically when the source data is refreshed.

## Author

**Manpreet Jadon**

