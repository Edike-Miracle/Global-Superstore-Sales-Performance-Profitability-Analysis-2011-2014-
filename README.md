# Global Superstore Sales Performance & Profitability Analysis (2011–2014)

## Project Overview
This project presents a comprehensive, end-to-end sales performance, profitability, and operational efficiency analysis of the **Global Superstore** dataset spanning from 2011 to 2014. The analysis evaluates over 25,000 global orders ($12.64M total revenue) to uncover critical profit leakages, evaluate promotional discount efficiency, measure shipping SLA compliance, and identify high-value vs. loss-making customer segments.

The primary objective is to equip executive leadership with data-driven insights to curb loss leaders, optimize discounting strategies, and maximize net profit margins without sacrificing sales growth.

---

## Data Sources
* **Primary Dataset:** `Global_Superstore2.xlsx`
* **Dataset Scope:** Global transaction logs containing order details, customer demographics, geographic locations, product categories/sub-categories, shipping modes, shipping costs, sales, profit, and discounts cost from 2011 through 2014.

---

## Tools & Technical Tool stack
* **Data Extraction & Transformation:** Microsoft Excel, Power Query (M Code)
* **Data Modeling:** Power Pivot (Star Schema configuration with 1-to-Many relationships)
* **Analytical Calculations:** DAX (Data Analysis Expressions) for explicit measures
* **Exploratory Data Analysis (EDA):** Excel Pivot Tables, Value Field Settings, Slicers
* **Data Visualization:** Interactive 3-Page Executive Excel Dashboards

---

## Data Cleaning & Transformations (Power Query)
All data cleaning and structural transformations were executed in **Power Query** to ensure reproducible data hygiene:

1. **Data Type Validation:** Enforced correct data types across numeric currency fields (`Sales`, `Profit`, `Shipping Cost`), dates (`Order Date`, `Ship Date`), and text dimensions.
2. **Dimension Table Extraction (Star Schema):** 
   * Created normalized dimension tables for **Customers (`DimCustomer`)**, **Products (`DimProduct`)**, **Orders (`DimOrder`)**, and **Geography (`DimGeo`)**.
   * Removed duplicates across primary key columns in all dimension tables to guarantee entity integrity.
3. **GeoKey Creation (Custom Key for Non-US Data):**
   * *Challenge:* Non-US countries in the dataset lacked postal codes, creating potential duplicate location keys.
   * *Solution:* Created a custom merged column (`GeoKey` = `City` & `State`) in Power Query to serve as a unique primary key for the `DimGeo` dimension table.
4. **Calculated Feature Engineering:**
   * **`Lead Time (Days)`:** Calculated duration between `Order Date` and `Ship Date`.
   * **`Is Loss Order`:** Conditional column flag identifying transactions resulting in negative net profit (`Profit < 0`).
   * **`Discount Tier`:** Conditional column categorizing discounting levels (`Normal Discount` < 20% vs. `High Discount` >= 20%).
   * **`Is On Time`:** Custom M conditional column evaluating shipping SLA compliance based on ship mode target thresholds:
     ```powerquery
     if [Ship Mode] = "Same Day" and [#"Lead Time (Days)"] <= 0 then 1
     else if [Ship Mode] = "First Class" and [#"Lead Time (Days)"] <= 2 then 1
     else if [Ship Mode] = "Second Class" and [#"Lead Time (Days)"] <= 3 then 1
     else if [Ship Mode] = "Standard Class" and [#"Lead Time (Days)"] <= 5 then 1
     else 0
     ```

---

## Data Modeling & DAX Measures
Using **Power Pivot**, a Star Schema model was established connecting the central **Fact Table** to normalized dimension tables via **1-to-Many Relationships**.

### Primary DAX Measures Used:

```dax
-- Total Revenue / Sales
Total Sales = SUM('Fact Table'[Sales])

-- Total Net Profit
Total Profit = SUM('Fact Table'[Profit])

-- Profit Margin Percentage
Profit Margin % = DIVIDE([Total Profit], [Total Sales], 0)

---

## Research Questions & Exploratory Analysis (EDA)

* **Annual & Seasonal Growth:** How do revenue and profit scale Year-Over-Year (YoY) and Month-Over-Month (MoM)?
* **Geographic Efficiency:** Which global regions generate top-line volume versus those driving the highest net profit margins?
* **Product Performance & Loss Leaders:** Which sub-categories generate the highest top-line revenue yet fail to yield net profits?
* **Promotional Discount Impact:** Does steep discounting (>= 20%) drive profitable volume or erode net profitability?
* **Operational Shipping SLAs:** What are the average lead times and on-time delivery rates across shipping modes?

---

## Report in Analysis & Dashboard Structure

The final output consists of a dynamic, 3-Page Executive Dashboard designed for leadership decision-making:

### Page 1: Executive & Financial Performance
* **Key Metrics:** Highlights global KPI cards (`Overall Profit Margin %: 11.61%`, `Average Discount Rate %: 14.29%`, `Average Order Value: $504.99`, `Count Order: 25,035`).
* **YoY Performance Matrix:** Displays revenue growth from **$2,259,451 (2011)** to **$4,299,866 (2014)** alongside profit growth trajectory.
* **Seasonality & Regional Volume vs. Margin:** Maps visual trend lines showing significant Q4 seasonal spikes (November–December) and contrasts volume vs. profit across global markets.

### Page 2: Product & Logistics Operations
* **Operational Metrics:** Tracks `On-Time Delivery Rate (67.75%)`, `Average Shipping Cost per Order ($26.38)`, and `Loss Rate (38.12%)`.
* **Sub-Category Deep Dive:** Itemizes revenue and profit across all product categories, identifying extreme negative drivers.
* **Lead Time Analysis:** Visualizes operational duration across `Same Day (0 days)`, `First Class (2 days)`, `Second Class (3 days)`, and `Standard Class (5 days)`.

### Page 3: Customer Behavior & Segmentation
* **Customer Distribution:** Segmented across `Consumer (818)`, `Corporate (476)`, and `Home Office (296)` cohorts.
* **Top Revenue Contributors:** Itemizes top-tier global customers driving significant volume (e.g., Tom Ashbrook with $40,488 sales, Tamara Chand with $37,457 sales).
* **Bottom Loss Accounts:** Flags specific accounts generating high negative profit due to heavily discounted furniture orders (e.g., Cindy Stewart -$6,152 loss).

---

## Key Results & Analytical Findings

1. **Geographic Performance (Volume vs. Efficiency):**
   * **Highest Volume:** **Central Region** leads global revenue with **$2,833,620** in sales (41,911 units).
   * **Highest Profit Margin Efficiency:** **Canada (26.62%)** and **North Asia (19.52%)** yield the highest net profit per dollar sold.
   * **The Disparity:** Central Region brings in massive top-line revenue but retains a lower profit margin (**11.09%**) due to high shipping burdens and frequent promotional discounting.

2. **Underperforming Products & "Loss Leaders":**
   * **Primary Loss Driver:** **Tables** is the largest loss sub-category globally, generating a net loss of **-$64,083.39** despite producing over **$757,000** in sales.
   * **Root Cause:** Tables are heavy/bulky (driving high shipping costs) and are routinely sold at steep discount rates.
   * **Profitable Stars:** **Copiers** ($256,568 profit) and **Phones** ($216,717 profit) drive the vast majority of overall company net income.

3. **Discount Correlation (The Profit Killer):**
   * **Normal Discounts (< 20%):** Yield a positive **+15.11% Profit Margin** across 141,465 items sold.
   * **High Discounts (>= 20%):** Yield a negative **-1.90% Profit Margin** across 36,847 items sold.
   * **Verdict:** High discounting fails to drive profitable incremental volume. While low/normal discounting retains a healthy 15.11% profit margin, pushing discounts into high tiers erodes profitability and turns orders net-negative across multiple territories.

4. **Operational & Shipping Performance:**
   * **Shipping Volume:** Standard Class accounts for nearly half of all freight expenditures ($614,630.76 out of $1.35M total shipping spend) with an average lead time of **5.00 days**.
   * **On-Time Delivery:** Overall SLA compliance stands at **67.75%**, leaving significant room for logistics optimization.

---

## Strategic Recommendations

* **Cap Promotional Discounts:** Enforce a strict policy capping discounts below **20%** globally—especially on bulky categories like Tables and Bookcases.
* **Restructure Pricing on Bulky Loss-Leaders:** Re-evaluate freight costs and base pricing for the **Tables** sub-category to offset shipping fees and eliminate the -$64k net loss.
* **Target High-Margin Regions:** Reallocate marketing budget toward high-margin regions (**Canada**, **North Asia**) to maximize net return on investment.
* **Customer-Level Discount Thresholds:** Restrict custom discount permissions for accounts purchasing heavily discounted furniture to protect margins generated from Technology sales.

---

## Data Limitations

* **Geographic Granularity:** Non-US states and cities lacked standardized postal codes, necessitating the creation of a composite key (`GeoKey`) in Power Query for data modeling.
* **Customer Acquisition Cost (CAC):** The dataset lacks customer acquisition costs and direct marketing expenses, limiting profit analysis strictly to gross/net sales profit minus shipping cost.

-- Average Order Value (AOV)
Average Order Value = DIVIDE([Total Sales], DISTINCTCOUNT('Fact Table'[Order ID]), 0)
