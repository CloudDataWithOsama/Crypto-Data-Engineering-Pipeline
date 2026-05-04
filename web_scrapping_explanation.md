
---

# Web Scraping Comparison: BeautifulSoup VS Selenium + BeautifulSoup

This document outlines the technical journey and the transition from a static scraping method to a dynamic hybrid approach for the CoinMarketCap project.

## 1. The Pure BeautifulSoup (Requests + BS4) Approach
In the initial phase, we used the standard `requests` library combined with `BeautifulSoup`.

### How it Works:
*   **HTTP Request:** The `requests` library sends a simple "GET" request to the URL and retrieves the static HTML code of the page.
*   **Parsing:** `BeautifulSoup` then searches through that HTML to find specific tags like `tr` (table rows) and `td` (table cells) to extract data.

### The Limitation:
*   **Lazy Loading:** CoinMarketCap uses JavaScript to load data dynamically as you scroll. When a script simply "requests" the page without scrolling, only about **14–15 coins** are visible in the HTML.
*   **Missing Data:** Because `requests` cannot interact with the page (it cannot scroll), it was impossible to capture the full list of 100 coins per page using this method alone.

---

## 2. The Selenium + BeautifulSoup (Hybrid) Approach
To solve the "Lazy Loading" issue, we moved to a hybrid model.

### How it Works:
1.  **Automated Browser:** We use `Selenium` to launch a real instance of the Chrome browser.
2.  **Simulated Scrolling:** We programmed the browser to scroll down the page in increments (using `window.scrollBy`).
3.  **Triggering JavaScript:** As Selenium scrolls, the website's JavaScript triggers the loading of all 100 rows.
4.  **Parsing the Full Source:** Once the page is fully loaded, we pass the *entire* rendered HTML to `BeautifulSoup` for high-speed data extraction.

### Benefits:
*   **Full Data Capture:** We can now reliably scrape all **100 coins** per page.
*   **Reliability:** Elements like "Volume" and "Market Cap" that only appear upon interaction are now easily accessible.

---

## 3. Key Technical Fixes Implemented
The script includes specific logic to handle the complexities of the CoinMarketCap table:

*   **Name Column:** We used multiple tag checks (`p` tags and `span` tags) to ensure the "Name" is captured regardless of slight UI changes, preventing "N/A" results.
*   **Volume(24h):** We targeted specific CSS classes (like `font_weight_500`) and added fallback logic to ensure this column is never blank.
*   **Market Cap Cleaning:** Since the site often includes hidden text for mobile views, we used string splitting (`split('$')`) to keep only the clean numerical values.
*   **Skip 1st Row:** Per the project requirements, the script automatically ignores the first row of the first page (usually the Index DTF) to keep the data focused on individual coins.

---

## Conclusion
While **BeautifulSoup** is faster for simple, static websites, a **Selenium + BeautifulSoup** hybrid is essential for modern, dynamic sites like CoinMarketCap to ensure data completeness and accuracy.