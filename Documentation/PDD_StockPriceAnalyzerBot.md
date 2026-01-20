# Process Design Document (PDD)
## Project Name
Stock Price Analyzer Bot – Yahoo Finance (UI Automation + Excel Automation)

## Objective
Automate collection of 1-year historical stock price data from Yahoo Finance for a list of companies provided in an input Excel file, compute key metrics, and update the same Excel file with results.

## Scope
### In Scope
- Read company list from Excel.
- Launch Chrome in Incognito mode.
- Navigate to Yahoo Finance portal.
- Search each company using company name/ticker.
- Navigate to Historical Data and retrieve 1-year data.
- Calculate:
  - Current Price (latest available close price)
  - Average Close Price (1-year)
  - Highest Close price and its date
  - Lowest Close price and its date
- Write computed values back to Excel.
- Close browser and Excel resources.

### Out of Scope
- API-based extraction (this is UI automation based).
- Real-time alerting/notifications.
- Advanced technical analysis indicators (RSI, MACD etc.).
- Handling of paywalled or restricted data sources.

## Assumptions
- Yahoo Finance is accessible without login.
- Historical Data table is available for the company.
- Input file contains valid tickers or searchable company names.
- System has Google Chrome installed.

## Constraints
- Website UI changes may require selector updates.
- Data extraction depends on table availability and load times.
- Captcha/anti-bot mechanisms may block automation in some cases.

## Process Flow (High-Level)
1. Read input Excel file.
2. Open Chrome in Incognito and navigate to Yahoo Finance.
3. For each row/company in Excel:
   - Search company
   - Open company page
   - Open Historical Data tab
   - Set time range to 1 year (if needed)
   - Extract historical prices table
   - Compute metrics
   - Update Excel row with output metrics and status
4. Save Excel output.
5. Close Chrome and cleanup.

## Input
### Input File
StockPriceBot_Input.xlsx

### Input Sheet
Sheet: Input

### Columns
- CompanyName (String) – Optional, used for readability
- Ticker (String) – Preferred search input
- Output columns (blank initially):
  - CurrentPrice
  - Avg1Y
  - HighPrice
  - HighDate
  - LowPrice
  - LowDate
  - Status
  - Error

## Output
Updated Excel file with calculated results in the same row as each company.

## Business Rules
- Use the latest available “Close” price as CurrentPrice.
- Average is computed from Close prices for the last 1 year.
- Highest/Lowest is based on Close prices.
- If extraction fails for a company:
  - Set Status = Failed
  - Populate Error with exception message
  - Continue to next company

## Exception Handling
- Website not reachable → fail current run (critical)
- Company not found → mark row failed, continue
- Historical data not available → mark row failed, continue
- Data conversion errors → mark row failed, continue

## Logging & Reporting
- Log start and end of process.
- Log each company processing start/end.
- Log exceptions with message and screenshot (optional).

## Security/Compliance
- No credentials are used.
- Data is publicly available market data.
- No personally identifiable information is processed.

## Estimated Run Time
~30–90 seconds per company depending on network speed and site responsiveness.
