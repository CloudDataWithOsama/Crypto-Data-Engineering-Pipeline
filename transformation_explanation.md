# Data Transformation & Engineering Explanation

This document explains the transformation pipeline applied to the raw CoinMarketCap data to make it ready for financial analysis and machine learning.

## 1. Data Cleaning (Normalization)
Raw data from web scraping is often "dirty" because it contains symbols and units intended for human readability but not for computer processing.

*   **Currency & Symbol Removal:** We stripped characters like `$`, `,`, and `%` from the `Price`, `Market Cap`, and `Percentage` columns.
*   **Data Type Conversion:** After removing symbols, we converted these columns from strings (text) to **Floats**. This allows us to perform mathematical operations like calculating averages or growth rates.

## 2. Unit Standardization (Suffix Handling)
CoinMarketCap uses shorthand notation like **'T'** for Trillions, **'B'** for Billions, and **'M'** for Millions.

*   **Conversion Logic:** We created a function to detect these suffixes and multiply the base number by the corresponding power of 10 (e.g., $1.5 \text{T} \times 1,000,000,000,000$).
*   **Result:** This standardizes the entire `Market Cap` column into absolute numerical values, enabling direct comparison between a Trillion-dollar asset like Bitcoin and Million-dollar Altcoins.

## 3. Feature Engineering (Actionable Insights)
Beyond cleaning, we added new columns to extract more value from the existing data.

*   **Volume to Market Cap Ratio (`Vol_MC_Ratio`):** 
    *   **Formula:** $\frac{\text{Volume (24h)}}{\text{Market Cap}}$.
    *   **Purpose:** This measures the "Liquidity" or "Trading Intensity" of a coin. A higher ratio indicates that the asset is being traded more frequently relative to its total supply.
*   **Market Capitalization Categorization:**
    *   **Method:** We used `pd.cut` to "bucket" coins into three tiers: **Small Cap**, **Mid Cap**, and **Large Cap** based on their Market Cap value.
    *   **Purpose:** This helps in risk assessment, as Large Cap coins (like Bitcoin/Ethereum) are generally considered more stable, while Small Cap coins are considered higher risk/higher reward.

## 4. Final Output
The transformed data is saved as `coinmarketcap_data_transformed.csv`. It is now perfectly structured for:
1.  **Visualizations:** Creating price trend charts or volume bars.
2.  **Dashboards:** Importing into tools like PowerBI, Tableau, or Excel.
3.  **Modeling:** Using the numeric features as inputs for predictive price models.