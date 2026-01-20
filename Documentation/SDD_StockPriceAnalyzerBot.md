# Solution Design Document (SDD)
## Project Name
Stock Price Analyzer Bot – Yahoo Finance

## Technology Stack
- UiPath Studio (Modern Experience)
- Chrome Browser Automation
- Excel Automation (Modern)
- DataTable processing and numeric calculations

## Architecture Overview
Main workflow follows standard structure:
- Main.xaml
  - Init.xaml
  - Process.xaml
  - End.xaml

## Workflow Details
### 1. Main.xaml
Responsibilities:
- Orchestrate Init → Process → End
- Global Try-Catch
- Final logging

### 2. Init.xaml
Responsibilities:
- Load configuration values (optional Config sheet / hardcoded constants)
- Validate input file exists
- Launch Chrome incognito and navigate to Yahoo Finance
- Open Excel file and load input sheet into DataTable

Outputs:
- dtCompanies (DataTable)
- Browser reference/window handle

### 3. Process.xaml
Loop:
- For Each Row in dtCompanies

Steps per company:
1. Extract search text:
   - Prefer Ticker, else CompanyName
2. Perform Yahoo Finance search
3. Open company details page
4. Navigate to Historical Data tab
5. Select time period = 1 year (if required)
6. Extract table data into dtHistory
7. Compute metrics:
   - CurrentPrice: close value from latest row
   - Avg1Y: average of close values
   - HighPrice/HighDate: max close and corresponding date
   - LowPrice/LowDate: min close and corresponding date
8. Populate output columns in current row
9. Mark Status = Success

Error handling per company (Try-Catch):
- On exception:
  - Status = Failed
  - Error = exception message
  - Continue next row

### 4. End.xaml
Responsibilities:
- Write updated dtCompanies back to Excel
- Save and close Excel scope
- Close browser tab/window
- Dispose resources

## Key Selectors / UI Objects (High Level)
- Yahoo Finance search textbox
- Search results first item / company link
- Historical Data tab
- Historical Data table (Date, Open, High, Low, Close)
- Time period dropdown (if applicable)

## Data Extraction Approach
- Use “Extract Table Data” activity for Historical Data table.
- Output: DataTable dtHistory

## Calculation Logic
Given dtHistory with columns including Date and Close:

1) Clean Close values:
- Remove commas
- Convert to Double

2) CurrentPrice:
- First row Close (latest date)

3) Avg1Y:
- Mean of Close values

4) HighPrice/HighDate:
- Maximum Close and its Date

5) LowPrice/LowDate:
- Minimum Close and its Date

## Non-Functional Requirements
- Reliability: Retry mechanism for page load/search actions
- Performance: Process and write Excel at the end (single write-back)
- Maintainability: Use reusable workflows (SearchCompany.xaml, ExtractHistoricalData.xaml, CalculateMetrics.xaml)

## Configurable Parameters (Recommended)
- YahooFinanceURL = https://finance.yahoo.com/
- TimeOutSeconds = 30
- MaxRetryCount = 2
- InputFilePath
- SheetName = Input

## Testing Strategy
### Test Cases
1. Valid ticker → success
2. Invalid ticker → failure with proper error
3. Network slow → retry works
4. No historical data table → fail row
5. Close values with comma formatting → conversion success
