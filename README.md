# SME Funding Analytics Pipeline 🚀

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![Google Sheets](https://img.shields.io/badge/Google_Sheets-34A853?style=for-the-badge&logo=googlesheets&logoColor=white)
![Fivetran](https://img.shields.io/badge/Fivetran-0066FF?style=for-the-badge&logo=fivetran&logoColor=white)
![Snowflake](https://img.shields.io/badge/Snowflake-29B5E8?style=for-the-badge&logo=snowflake&logoColor=white)

> An automated ELT pipeline built to ingest, clean, and aggregate Australian government contract data for Business Intelligence (BI) reporting. Designed to mirror the data infrastructure of a fast-growing SME lending fintech.

<details>
<summary>📖 <strong>Table of Contents (Click to expand)</strong></summary>

1. [Business Value](#-business-value)
2. [1. Data Extraction & Cleaning](#1-data-extraction--cleaning-python)
3. [2. Automated Ingestion](#2-automated-ingestion-fivetran)
4. [3. Transformation Layer](#3-transformation-layer-snowflake)
</details>

---

## 💼 Business Value
By automating the ingestion and transformation of this public contract data, the **Insights Team** can immediately identify which government sectors are awarding the most SME contracts. This allows the lending team to target high-growth industries (such as IT or Construction) for flexible funding and loan solutions based on verified contract cash flows.

---

## 1. Data Extraction & Cleaning (Python)
Public government data often contains formatting inconsistencies. Using **Python and Pandas** via Google Colab, I extracted real Australian SME contract data from the `data.gov.au` API. 

* **The Challenge:** Legacy `.xls` files that were actually TSV files masquerading as Excel, with nested text formatting.
* **The Solution:** A Python script that standardizes encoding (`cp1252`), formats column headers, and exports a clean `.csv`.

---

## 2. Automated Ingestion (Fivetran)
The cleaned dataset is staged in Google Sheets and ingested directly into Snowflake using **Fivetran's SaaS deployment**. 

| Google Sheets Source Setup | Fivetran Connection Mapping |
| :---: | :---: |
| ![Google Sheet Source](images/GoogleSheetConnection.png) | ![Fivetran Connection Setup](images/FiveTranConnection.png) |

| Snowflake Destination Grants | Fivetran Sync Successful |
| :---: | :---: |
| ![Snowflake Destination Setup](images/FiveTranDestinationSetup.png) | ![Fivetran Sync Successful](images/DataSync.png) |

---

## 3. Transformation Layer (Snowflake)
To ensure near real-time data availability without manual orchestration, I built a 3-layer architecture using **Snowflake Dynamic Tables** with a `TARGET_LAG` of 1 hour.

### 🏗️ Pipeline Architecture

![Snowflake SQL Architecture](images/SnoflakeSqlFiles.png)

<details>
<summary>🔍 <strong>View the 3-Layer Logic (Click to expand)</strong></summary>

1. **`STG_AU_CONTRACTS` (Staging):** Standardizes column names, casts financial values to `NUMBER(38,2)`, and filters null records.
2. **`INT_SME_FUNDING` (Intermediate):** Filters out massive corporate contracts, categorizing remaining data into SME lending tiers: `Micro ($10k-$50k)`, `Small ($50k-$200k)`, and `Medium ($200k-$500k)`.
3. **`MART_AGENCY_SME_DEMAND` (Mart):** The final aggregated view delivering total funding volume and average contract size by government agency and SME tier.
</details>

<br>

### 📊 Final BI-Ready Output

![Snowflake Final Mart](images/SnowflakeResults.png)
*Final output of the `MART_AGENCY_SME_DEMAND` dynamic table, ready to be connected to Tableau or PowerBI.*
