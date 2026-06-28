# Credit-Risk-Portfolio-Modelling
End-to-end data pipeline and risk modeling infrastructure analyzing 2.3M credit records. Computes volume-weighted PAR, FICO stratification, LGD severity, Loss Given Default, Loss Acceleration Velocity (LAV), Risk Interaction Multipliers (RIM), and historical Credit Drift Acceleration (CDA).

Executive Summary

Traditional credit models evaluate risk by counting individual defaults, which can mask the true scale of capital loss. This project builds a volume-weighted framework focused on actual principal dollars at risk. By tracking cash exposure across FICO bands, debt-to-income (DTI) ratios, and loan terms, this infrastructure uncovers true capital impairment across historical credit cycles.

The 6 Core Risk Analytics Layers

1. Portfolio at Risk (PAR %)

Purpose: Tracks true capital loss velocity by funding volume instead of raw loan counts.
Logic: Calculated inside SQL by dividing total charged-off loan amounts by total funded capital, grouped by risk grade (`WHERE loan_status IN ('Fully Paid', 'Charged Off')`).

2. Loss Given Default (LGD %) Post-Default Severity

Purpose: Quantifies net capital permanently wiped out after a default occurs.
Logic: Isolates `Charged Off` records and measures total recoveries against gross principal charged off to calculate net loss severity ($LGD = 100\% - \text{Recovery Rate \%}$).

3. Loss Acceleration Velocity (LAV) Horizon Multiplier

Purpose: Measures duration risk by evaluating how fast defaults accelerate on long-term contracts.
Logic: Uses SQL Common Table Expressions (CTEs) to divide the volume-weighted PAR % of 60-month loans by the PAR % of 36-month loans across identical risk grades.

4. Capital Impairment Velocity (CIV) Control Index

Purpose: Benchmarks portfolio vulnerability to borrower debt leverage.
Logic: Establishes a baseline using low-debt borrowers (`dti < 10.0`). It divides the PAR % of higher leverage tiers by this control baseline to index risk acceleration.

5. FICO x DTI Risk Interaction Multiplier (RIM) Matrix

Purpose: Isolates compounding risk intersections where low credit scores meet high debt.
Logic: A cross-sectional grid mapping 5 FICO bands against 4 DTI tiers. It multiplies cell PAR % by average DTI, indexing the result against the safest cell (Super Prime + Low Leverage).

6. Credit Drift Acceleration (CDA) Velocity Timeline

Purpose: Measures chronological underwriting degradation across origination vintages.
Logic: Extracts the origination year from `issue_d` and divides the current year's vintage PAR % by the prior year's PAR % to track risk acceleration against a 1.00x stability baseline.

Data Pipeline & Technical Architecture

Cloud Ingestion: Connects to Google Drive for data persistence and automates Kaggle API credentials to stream the source dataset safely.
Memory-Safe ETL: Streams raw data in chunks of 100,000 rows, stripping formatting errors and dropping records missing critical inputs (`loan_status`, `grade`, `loan_amnt`, `int_rate`) to prevent memory crashes.
Local Data Warehouse: Commits sanitized data blocks directly into an optimized local SQLite database table named `loans_ledger`.
Push-Down SQL Optimization: Offloads heavy risk matrix math to the SQL engine using multi-table joins, subqueries, and conditional `CASE WHEN` logic, keeping Python dataframes lightweight.
Visual Engine: Feeds database summaries into Matplotlib and Seaborn to compile a publication-quality 12-panel dashboard saved locally at 300 DPI.

Final Credit Portfolio Risk Dashboard
This visual interface serves as the master control screen for the entire 2.3M record risk engine:

Enterprise Scaling Strategy

1. Index Optimization: Appending B-Tree indexes to high-frequency query partition columns (`grade`, `loan_status`, `dti`) to minimize query execution times as data scales.
2. Cloud Migration: Swapping out local SQLite binary storage for enterprise cloud data warehouses like PostgreSQL, Snowflake, or Google BigQuery to support large concurrent team workloads.
3. Workflow Automation: Packaging pipeline script cells into orchestrators like Apache Airflow to refresh the 12-panel dashboard automatically as new daily transaction files append.


