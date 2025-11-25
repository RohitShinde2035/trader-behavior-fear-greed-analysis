# Trader Behavior vs Bitcoin Fear & Greed Index

This repository contains my solution for the **"Junior Data Scientist – Trader Behavior Insights"** assignment.

The goal of this project is to explore the relationship between **market sentiment** (Bitcoin Fear & Greed Index) and **trader performance** (Hyperliquid historical data), and to extract insights that can inform smarter trading strategies.

---

## 📂 Project Structure

- `trader_behavior_fear_greed_analysis.ipynb`  
  Main Jupyter notebook.  
  - Loads trader data and Fear & Greed data  
  - Cleans and preprocesses timestamps and numeric fields  
  - Merges trader data with daily sentiment  
  - Computes performance and risk metrics by sentiment regime  
  - Produces visualizations and summary tables

- `requirements.txt`  
  Python dependencies required to run the notebook.

> **Note:** The raw CSV files are not uploaded here to keep the repository lightweight and respect data ownership.  
> The notebook assumes you have downloaded the datasets from the original Google Drive links provided in the assignment.

---

## 📊 Problem Statement

We are given:

1. **Bitcoin Market Sentiment Dataset**  
   - Columns: `timestamp`, `value`, `classification`, `date`  
   - Represents daily sentiment such as `Extreme Fear`, `Fear`, `Neutral`, `Greed`, `Extreme Greed`.

2. **Historical Trader Data from Hyperliquid**  
   - Columns include (sample):  
     `Account`, `Coin`, `Execution Price`, `Size Tokens`, `Size USD`,  
     `Side`, `Timestamp IST`, `Closed PnL`, `Fee`, `Timestamp`, etc.

**Objective:**  
Link trader performance to market sentiment and analyze:
- How do PnL, win rate, and risk-adjusted returns change across sentiment regimes?
- Which traders and symbols perform best in Fear vs Greed environments?
- How does position sizing behave under different market emotions?

---

## 🛠️ Methodology

1. **Data Cleaning & Preprocessing**
   - Parsed `Timestamp IST` (DD-MM-YYYY HH:MM) into a proper `datetime` column.
   - Created a normalized `date` column (no time).
   - Converted numeric fields (`Execution Price`, `Size Tokens`, `Size USD`, `Closed PnL`, `Fee`) to numeric types.
   - Derived `notional = Execution Price × |Size Tokens|` as a proxy for trade exposure.

   For the Fear & Greed dataset:
   - Converted UNIX `timestamp` (seconds) to `datetime`.
   - Normalized to daily `date`.
   - Kept only `date` + `classification` for merging.

2. **Merging Sentiment with Trades**
   - Performed a `left` join on the `date` column so that every trade gets the sentiment of that day:
     ```python
     merged = trades.merge(fg_simple, on="date", how="left")
     ```

3. **Performance Metrics by Sentiment**
   For each sentiment bucket (`Extreme Fear`, `Fear`, `Neutral`, `Greed`, `Extreme Greed`), the notebook computes:
   - `n_trades` – number of trades  
   - `total_pnl` – aggregate Closed PnL  
   - `avg_pnl` – average PnL per trade  
   - `std_pnl` – volatility of PnL  
   - `sharpe_like` – `avg_pnl / std_pnl` as a simple risk-adjusted metric  
   - `win_rate`, `loss_rate`, `breakeven_rate`  
   - `avg_notional` – average trade exposure

4. **Deeper Cuts**
   - **Account-level analysis:** which traders perform best in each regime, and what is each account’s “best” sentiment environment?
   - **Symbol-level analysis:** which coins perform best under `Greed` vs `Fear`?
   - **Time-series view:** daily PnL aggregated by sentiment over time.

5. **Visualization**
   The notebook produces:
   - Bar plots of average PnL and win rate per sentiment bucket.
   - Boxplots of PnL distribution by sentiment (with clipping to reduce the impact of outliers).
   - A time-series plot of daily total PnL, colored by sentiment.

---

## 📌 Key Insights (Example)

These are indicative and will depend on the actual data:

- **Extreme Greed** days show the best **risk-adjusted performance** (highest Sharpe-like ratio).  
- **Extreme Fear** days tend to have the weakest performance, with lower win rates and high PnL volatility.  
- A large share of trades are **breakeven**, indicating frequent scalping / tight exits.  
- Position sizing (`avg_notional`) tends to increase under **Fear**, possibly reflecting higher volatility and more opportunity.

These patterns suggest that **trader behavior is strongly influenced by market sentiment**, and that strategies could be adapted per regime (e.g., size down in Extreme Fear, exploit momentum in Greed).

---

## ▶️ How to Run

1. Clone the repository:
   ```bash
   git clone https://github.com/<your-username>/trader-behavior-fear-greed-analysis.git
   cd trader-behavior-fear-greed-analysis
   
2. Install Dependencies
pip install -r requirements.txt

3. Download Data Files
   
   https://drive.usercontent.google.com/download?id=1IAfLZwu6rJzyWKgBToqwSmmVYU6VbjVs&export=download&authuser=0
   
   https://drive.usercontent.google.com/download?id=1PgQC0tO8XN-wqkNyghWc_-mnrYv_nhSf&export=download&authuser=0

5. Run the Notebook
jupyter notebook

Then open:

trader_behavior_fear_greed_analysis.ipynb

Run all cells to reproduce the full analysis.
