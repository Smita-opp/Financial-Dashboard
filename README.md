
# Financial Dshaboard

## Steps followed 

### Step 1 : Launch Power BI Desktop and start a new report.
### Step 2 : 
 ### Get Data:

- Click on "Get Data" in the Home ribbon.
- Choose the data source you want to connect to  Excel.
- Import our financial data into Power BI.

### Data Transformation
- Use the Power Query Editor to clean and transform your data. This might include       filtering rows, removing duplicates, and performing calculations.
- Click on "Transform Data" to open the Power Query Editor.
- Make the necessary transformations and click "Close & Apply".

### Design the Dashboard
-Create the Date Table 
 
    Measure
    dimDate = 
    VAR _MinYear = 2022
    VAR _MaxYear = 2022
    VAR _Dates = CALENDAR(DATE(_MinYear,1,1),DATE(_MaxYear,12,31))
    VAR _CalendarTbl =
    ADDCOLUMNS(
    _Dates,
    "Year", YEAR([Date]),
    "QuarterNo", FORMAT([Date], "\QQ"),
    "Quarter", FORMAT([Date], "\QQ"),
    "MonthNo", MONTH([Date]),
    "Month", FORMAT([Date], "MMM"),
    "WeekdayNo", WEEKDAY([Date]),
    "Weekday", FORMAT([Date], "DDD"),
    "Period", MONTH([Date]) & "-" &  YEAR([Date])  
        )
      RETURN
          _CalendarTbl  


- Create Relationships :

Create relationships between Data And Financial tables to ensure accurate data analysis.
Go to the "Model" view and drag and drop fields to create relationships.

- Create Visualizations:

Use the Visualization pane to add various types of charts and graphs (e.g., Card, pie charts, Matrix, etc.).
Drag fields from the Fields pane to the Values,  Legend, and other boxes in the Visualization pane to build your visualizations.

- Add Interactive Elements:

Use slicers, filters, and drill-through options to add interactivity to your dashboard.
For example, you can add a date slicer to filter data by different time periods.

- Design the Layout:

Arrange your visualizations on the report canvas to create a cohesive and easy-to-read dashboard.
Use titles, labels to make the dashboard more informative.

### Publish and Share the Dashboard

-Save Your Report:

Save your Power BI report file (.pbix) locally.


- Publish to Power BI Service:

Click on the "Publish" button in the Home ribbon.
Sign in to your Power BI account and select the workspace where you want to publish the report

## Visualizations for a Financial Dashboard

### Expense Breakdown:

Pie chart  showing the breakdown of cost of goods sold by category.
Donut chart showing the breakdown of Operating expenses by category.


### Key Financial Ratios:

Card visualizations for displaying key financial metrics like Sales, Income before Tax, and Net Income.

### P & L Statement

Matrix with with detailed Profit & Loss Statement

 ## DAX expression was written for the report
       Financial Value = 
    VAR PosNeg = MAX('Financial Statement Structure'[Expense])
    VAR Result = 
       IF(
          [Sales]<>0,
            IF(
               ISINSCOPE('Financial Statement Structure'[Level 2 Item]) ,
                  SWITCH(
                      SELECTEDVALUE('Financial Statement Structure'[Line Item ID]),
                "COGS - Labor", FORMAT([COGS - Labor], "#,##0,,.0")*PosNeg,
                "COGS - Materials", FORMAT([COGS - Materials], "#,##0,,.0")*PosNeg,    
                "COGS - Overhead", FORMAT([COGS - Overhead], "#,##0,,.0")*PosNeg,
                "OPEX - Administrative Expenses", FORMAT([OPEX - Administrative Expenses], "#,##0,,.0")*PosNeg,
                "OPEX - Depreciation and Amortization", FORMAT([OPEX - Depreciation and Amortization], "#,##0,,.0")*PosNeg,
                "OPEX - Selling Expenses", FORMAT([OPEX - Selling Expenses], "#,##0,,.0")*PosNeg,
                "Other - Interest Revenue", FORMAT([Other - Interest Revenue], "#,##0,,.0")*PosNeg,
                "Other - Interest Expense", FORMAT([Other - Interest Expense], "#,##0,,.0")*PosNeg,  
                "Other - Extraordinary Items", FORMAT([Other - Extraordinary Items], "#,##0,,.0")*PosNeg
                
        ),
        SWITCH(
            SELECTEDVALUE('Financial Statement Structure'[Level 1 Item]),
            "Other Income & Expenses",FORMAT([Other], "#,##0,,.0")*PosNeg,
            "Sales", FORMAT([Sales], "#,##0,,.0")*PosNeg,
            "Cost of goods sold", FORMAT([COGS], "#,##0,,.0")*PosNeg,
            "Operating expenses", FORMAT([OPEX], "#,##0,,.0")*PosNeg,
            "Gross Margin", FORMAT([Gross Margin], "#,##0,,.0")*PosNeg,
            "Income Before Tax", FORMAT([Income Before Tax], "#,##0,,.0")*PosNeg,
            "Operating Income", FORMAT([Operating Income], "#,##0,,.0")*PosNeg,
            "Income Tax", FORMAT([Income Tax], "#,##0,,.0")*PosNeg,
            "Net Income", FORMAT([Net Income], "#,##0,,.0")*PosNeg
        )
    )
     )
        RETURN
        Result


-       Financial Value - Current Period = 
        VAR CurrentPeriod_Year = MAX(dimDate[Year])
        VAR CurrentPeriod_Month = MAX(dimDate[MonthNo])
         VAR Result =
        CALCULATE(
          [Financial Value],
           dimDate[Year] = CurrentPeriod_Year,
          dimDate[MonthNo] = CurrentPeriod_Month
        )
      RETURN
       Result


-       Financial Value - Previous Period = 
        VAR MaxDate = EOMONTH(MAX(dimDate[Date]), -1)
        VAR PrevPeriod_Year = YEAR(MaxDate)
        VAR PrevPeriod_Month = MONTH(MaxDate)
        VAR Result =
        IF( NOT(ISBLANK([Financial Value])),
        CALCULATE(
        [Financial Value],
        REMOVEFILTERS(dimDate),
        dimDate[Year] = PrevPeriod_Year,
        dimDate[MonthNo] = PrevPeriod_Month
           )
             )
        RETURN
        Result

-       Financial Value % of Sales = 
        DIVIDE(
        ([Financial Value] * 1000000),
        [Sales]
         )

# Insights

A single page report was created on Power BI Desktop & it was then published to Power BI Service.


# Snapshot of Dashboard (Power BI Service)

![Finance Dashaboard](https://github.com/Smita-opp/Financial-Dashboard/assets/159506053/cddfcdce-9fa9-4064-beeb-9d8916b35934)

