# 💸 Expense Intelligence & Analytics Dashboard

A Python dashboard that cleans raw expense data with **Pandas and NumPy** and turns it into interactive spending insights using **Streamlit**.

## Features

- **Data preprocessing**: standardises column names, parses dates, cleans messy amounts (`Rs. 1,200`, `₹450`), removes invalid rows and duplicates, normalises categories, and generates a data-quality report.
- **Spending analytics**: headline KPIs, monthly trend with month-over-month change, category breakdown, top merchants, and average spend by weekday.
- **Anomaly detection**: flags unusually large transactions using a per-category z-score.
- **Forecasting**: simple linear-trend forecast of next month's spend using `numpy.polyfit`.
- **Budget tracking**: set a monthly budget per category and compare it with actual spend.
- **Interactive dashboard**: CSV upload, date and category filters, anomaly sensitivity slider, and cleaned-CSV download.

## Tech Stack

Python · Pandas · NumPy · Streamlit

## Project Structure

```
expense-intelligence-dashboard/
├── app.py                       # Streamlit dashboard
├── src/
│   ├── data_processing.py       # loading + cleaning (Pandas / NumPy)
│   └── analytics.py             # KPIs, trends, anomalies, forecast, budgets
├── data/
│   ├── generate_sample_data.py  # creates synthetic sample data
│   └── sample_expenses.csv
├── tests/
│   └── test_analytics.py
└── requirements.txt
```

## Getting Started

```bash
# 1. Clone the repository
git clone https://github.com/Shraddhhha/expense-dashboard.git
cd expense-dashboard

# 2. (Optional) create a virtual environment
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run the dashboard
streamlit run app.py
```

The app opens at `http://localhost:8501` with sample data loaded. Upload your own CSV from the sidebar.

## Input Format

Your CSV needs these columns (names are case-insensitive):

| date       | description | category | amount |
|------------|-------------|----------|--------|
| 2026-01-05 | Swiggy      | Food     | 420.50 |

Amounts may include `₹`, `Rs.` or commas.

## How It Works

**Data cleaning**
1. Column names are lower-cased and trimmed.
2. Dates are parsed with `pd.to_datetime(errors="coerce")`.
3. Amounts are cleaned with a regex and converted to floats.
4. Rows with invalid dates or amounts, non-positive amounts, and duplicates are removed and counted in the data-quality report.
5. Missing categories become "Uncategorized"; category text is trimmed and title-cased.

**Anomaly detection**

For each transaction:

```
z = (amount - category_mean) / category_std
```

A transaction is flagged when `z` is at or above the chosen threshold (default 3). Categories with fewer than 5 transactions are skipped because the statistic is unreliable on so little data.

**Forecast**

A straight line `y = a·x + b` is fitted to the monthly totals with `numpy.polyfit` and extended by one month. It needs at least 3 months of data. It captures the overall trend only, not seasonality.

## Run in Google Colab

Upload `expense_dashboard_colab.ipynb` to Colab and run all cells. It writes all project files, runs the tests, and shows the charts inline.

## Run Tests

```bash
python -m pytest -q
```

## Sample Data

`data/sample_expenses.csv` is **synthetic** (generated with a fixed random seed) and intentionally contains messy values so the cleaning pipeline has real work to do. Regenerate it with:

```bash
python data/generate_sample_data.py
```

## Future Improvements

- Auto-categorise transactions with a text classifier (TF-IDF + Logistic Regression)
- Isolation Forest for anomaly detection
- Seasonal forecasting (e.g. Holt-Winters)
- SQLite/PostgreSQL storage
- Recurring-expense detection

## Author

**Shraddha Suresh**

