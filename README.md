#  SGD/IDR Exchange Rate Analytics Dashboard

An end-to-end data engineering and business intelligence project that programmatically extracts and processes historical Singapore Dollar (SGD) to Indonesian Rupiah (IDR) exchange rates using Python, streams the pipeline into Google Sheets, and visualizes interactive financial insights in Power BI.

Developer: **Samuel Alvincent**

---

##  Project Architecture
[Yahoo Finance API] ➡️ [Google Colab / Pandas Data Pipeline] ➡️ [Google Sheets Cloud API] ➡️ [Power BI Desktop Engine] ➡️ [Interactive Dashboard]

---

##  Phase 1: Data Engineering Pipeline (`hacktiv8_currency_sgd_idr.ipynb`)

The data preparation layer is automated inside a Jupyter Notebook using **Python 3** and **Pandas**. The script handles automated extraction, feature engineering, and cloud data distribution:

### 1. Automated Ingestion via yfinance
* **Data Source:** Extracts 5 years of historical financial records (`period="5y"`) for the SGD/IDR ticker (`SGDIDR=X`) straight from Yahoo Finance API.
* **Structure Adjustments:** Cleans multi-level column indexes by flattening ticker tags (`df.columns.get_level_values(0)`) and resets the date index (`df.reset_index()`) to expose the `Date` object for parsing.

### 2. Time-Intelligence Feature Engineering
To build temporal and seasonal analysis capabilities for the BI dashboard, the timeline profile is broken down into structured dimensions:
```python
# Extracting exact time-intelligence features
df["year"] = df["Date"].dt.year
df["month"] = df["Date"].dt.month
df["month_name"] = df["Date"].dt.strftime("%B")
df["quarter"] = df["Date"].dt.quarter

```
## 3. Rate Velocity Metric (Daily_Change)
The net movement of the currency value compared to the previous trading session is computed inside the Python layer using the .diff() operation:

```python
# Calculating currency daily strength / weakness velocity
df["Daily_Change"] = df["Close"].diff()
```
  - Note: A positive output indicates SGD strengthening against IDR, while a negative value highlights weakening performance.

## 4. Cloud Data Ingestion (Google Sheets Pipeline)
Instead of writing static local CSV files, the data engineering pipeline authenticates into the Google Cloud environment via google.auth and updates a shared Google Spreadsheet live using gspread:
creds, _ = default()
gc = gspread.authorize(creds)
```python
sheet = gc.open_by_url("[https://docs.google.com/spreadsheets/d/1N666zze7Ys56IK8YBcrqdSoWR-rsW7yCMjxZR8nHjQU/edit?gid=452756811](https://docs.google.com/spreadsheets/d/1N666zze7Ys56IK8YBcrqdSoWR-rsW7yCMjxZR8nHjQU/edit?gid=452756811)")
```
---
#  Phase 2: Power BI Dashboard Architecture
The reporting engine pulls the processed dataset directly from the Cloud spreadsheet layer, distributing insights through an optimized "Summary to Detail" visual framework.

## 1. Analytical Core KPIs
- **Latest Open & Close Price:** Displays current operational boundaries for exchange operations.

- **Period Highest & Lowest Rate:** Represents absolute macro market thresholds (historical ceiling and floor).

- **Max Historical Volatility:** Identifies structural high-risk markers by identifying the biggest trading gap anomalies.

## 2. Visual Framework Mapping
- **SGD/IDR Historical Exchange Rate Trend (Area Chart):** High-level view showing the macroeconomic trend of currency valuations across multiple years.

- **Monthly Volatility Peak (Clustered Column Chart):** Outfitted with Conditional Formatting (Color Gradients) to automatically draw eyes to calendar months suffering from high historical volatility.

- **Quarterly Average Price Share (Donut Chart):** Pinpoints recurring currency strength spikes grouped by financial quarters (Q1–Q4).

- **Annual Max Volatility Trend (Line Chart):** Maps long-term volatility vectors to evaluate whether market velocity is contracting or expanding over time.

- **Global Navigation Year Filters (Slicer Grid):** Integrated at the top-right header region as interactive grid button tiles to enable effortless cross-filtering across the entire application interface.

# 🔗 How the Connection Works (Cloud Pipeline Architecture)
- The data pipeline execution cell inside the .ipynb file runs, parsing fresh financial market structures.

- gspread_dataframe overwrites the destination worksheet in Google Sheets.

- Power BI Desktop maintains a live web connector or Google Sheets connector targeting that specific sheet URL.

- Clicking Refresh in Power BI automatically triggers a data query directly to the cloud spreadsheet without requiring any manual manual CSV file exports.
---
# Local Deployment Guide (Run it Yourself)
Replicate the environment and execute the project workflow locally by following these deployment steps:

## Prerequisites
- Windows OS (Required to execute Microsoft Power BI Desktop).

- Python 3.8+ or a Google Colab instance.

- Access token/permissions to write data into Google Drive/Google Sheets

## Step 1: Clone the Project Repository
```bash
git clone [https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git](https://github.com/YOUR_USERNAME/YOUR_REPO_NAME.git)
cd YOUR_REPO_NAME
```
## Step 2: Configure and Execute the Data Engineering Stream
- Open **hacktiv8_currency_sgd_idr.ipynb** inside Google Colab or your local Jupyter environment.

- Make sure you install the dependencies: **!pip install gspread gspread_dataframe yfinance.**

- Update the sheet = **gc.open_by_url("YOUR_SPREADSHEET_URL")** string with a destination URL for a Google Spreadsheet you own.

- Run all cells to authenticate your Google profile and pipe data into your cloud worksheet.

## Step 3: Link Power BI Data Layer
- Open the Power BI dashboard file **(.pbix).**

- If data loading breaks due to credentials, **go to Home ➡️ Transform Data ➡️ Data source settings.**

- Select the data origin, click **Change Source...,** and paste your Google Spreadsheet link.

- Authenticate using your Google credentials and hit Close & Apply.

## Step 4: Automating New Cycles
Whenever you want to pull fresh currency metrics into your dashboard, simply re-execute the Python notebook cells and click Refresh inside the Power BI ribbon toolbar.
