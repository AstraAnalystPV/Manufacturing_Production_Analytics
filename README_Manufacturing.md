# Manufacturing Production Dashboard (Power BI)

A Power BI dashboard for tracking production, rejections, machine and employee performance, and customer orders in a label-manufacturing plant (woven and printed labels).

## Overview

The dashboard turns raw shop-floor production records into a stakeholder-ready report. It answers questions such as:

- How much did we manufacture, produce and reject, and how much is still pending (balance quantity)?
- Which products, machines and employees account for the most rejections?
- Which machines and employees produce the most?
- How does production split across delivery periods (Early, On Time, Late, Under Production)?
- Which customers place the most orders?

The scope comes from the briefing deck `Manufacturing_Analytics_on.pptx`, which lists these dashboard needs: manufactured, rejected, processed and wastage quantity; employee-wise and machine-wise rejections; production trend; manufactured vs rejected; and department-wise manufactured vs rejected.

## Files

| File | Description |
|---|---|
| `Dashboard_Manuf__Analyst.pbix` | Power BI report (7 pages) |
| `PROD_DATA.txt` | Source data: tab-delimited, UTF-16 encoded, 10,679 rows x 68 columns |
| `COLUMN_DISCRIPTION_OF_MANUFECTURING_DATA.docx` | Data dictionary describing every column |
| `metrics_list.xlsx` | List of 10 measures with their formulas |
| `Manufacturing_Analytics_on.pptx` | Short briefing deck on the dashboard requirements |

## Data

- **Source:** `PROD_DATA.txt`, one row per production transaction.
- **Period:** 1-27 November 2015 (Fiscal Year 2015 only).
- **Departments:** Woven Labels and Printed Labels.
- **Coverage:** 10 operations (for example Weaving, Printing, Cut & Fold, Cross Checking, Packing, Ultrasonic), 87 machines, 121 employees, 347 customers, about 1,160 items and about 2,750 work orders.
- **Date format:** dates are day/month/year (for example `09/11/15` is 9 November 2015). Set the locale or date format accordingly when importing, or November will be misread as January-December.
- **Loading tip:** the file is UTF-16 with tab separators. In Power BI choose "Text/CSV", set the delimiter to Tab and the file origin to Unicode.

### Key Columns

| Group | Columns |
|---|---|
| Quantities | `Manufactured Qty`, `Produced Qty`, `Processed Qty`, `Rejected Qty`, `Balance Qty`, `WO Qty`, `Final Processed Qty`, `Total Qty`, `Total Value` |
| Product | `Item Code`, `Item Name`, `Variant Name`, `Form Type`, `Department Name` |
| Process | `Operation Code`, `Operation Name`, `Work Centre Code`, `Work Centre Name`, `Is Final Process`, `Start Time`, `End Time` |
| People and machines | `Emp Name`, `EMP Code`, `Machine Code`, `Machine Name`, `Shift Code` |
| Orders | `SO Num`, `WO Number`, `WO Status`, `Cust Name`, `Buyer`, `Delivery Period`, `SO Delivery Date` |
| Time | `Doc Date`, `Fiscal Date`, `Fiscal Year`, `WO Date` |

The full list of 68 columns with descriptions is in the data dictionary.

## Dashboard Pages

| Page | Contents |
|---|---|
| **Dashboard** (main) | Title, five KPI cards, slicers (Item Name, Machine Name, Department, Operation Code, Employee), and visuals for production by delivery period, production by year, top 10 products by rejections, rejection trend, top 10 employees and top 5 machines by production, and a product-wise quantity waterfall |
| **Cards** | KPI cards: Total Rejected Qty, Total Manufacturing Qty, Total Production Qty, Total Order, Total Balance Qty |
| **Machine performance** | Production trend over time, manufactured vs produced by item, top 5 machines by production, machine rejection |
| **Employee Performance** | Top 10 employees by production, rejection by employee |
| **Orders & Customers** | Top 20 customers by orders, top 10 product-wise quantity (produced vs work-order quantity) |
| **Quality Dashboard** | Top 10 products with highest rejected quantity, rejection trend |
| **Time Analysis** | Production by delivery period, production by fiscal year |
| **Slicers** | Reusable filter panel: machine, item, department, employee, operation code |

## Measures

`metrics_list.xlsx` documents the following measures. They were written as Tableau expressions, so they need to be rebuilt as DAX in Power BI if they are not already in the model.

| Measure | Logic |
|---|---|
| Processed Qty | `Manufactured Qty - Rejected Qty` |
| Wastage % | `SUM(Rejected Qty) / SUM(Processed Qty)` |
| Total Wastage % | `Rejected Qty / (Manufactured Qty - Rejected Qty)` |
| Final Processed Qty | `Processed Qty` where `Is Final Process = TRUE` |
| Date Rejected Qty | `Rejected Qty` for a selected date, otherwise 0 |
| Rejected Qty F | Rejected quantity fixed at work order and form type level |
| Delivery Percentage | Distinct work orders per delivery period |
| Total manufactured qty | Switches between MTD, YTD and selected-date quantity |
| Estimated days | `(SO Qty Value / avg Total Qty Ordered) x avg Days to produce SO` |
| Total Manufacturing Cost | Listed without a formula |

### Data Snapshot

Totals computed directly from `PROD_DATA.txt`, useful for checking the dashboard cards:

| Measure | Total |
|---|---|
| Manufactured Qty | 86,766,796 |
| Produced Qty | 124,695,304 |
| Rejected Qty | 524,729 |
| Processed Qty (as the file stores it) | 86,242,067 |
| Balance Qty | 30,454,140 |
| WO Qty | 172,590,041 |

## How to Use

1. Open `Dashboard_Manuf__Analyst.pbix` in Power BI Desktop.
2. If prompted, update the data source path to the location of `PROD_DATA.txt`, then refresh.
3. Use the slicers to filter by item, machine, department, operation or employee. Clicking a chart element cross-filters the other visuals on the page.
4. Navigate between pages with the page tabs.

## Things to Check

- **Measure definitions.** I could not read the measures stored inside the `.pbix` (the data model is compressed), so the Measures section is based on `metrics_list.xlsx`. Check that the dashboard cards use the same logic.
- **Single fiscal year.** All data is from 2015, so "Production by Year" shows one slice. A monthly or weekly breakdown would be more informative.
- **Page names and duplicates.** Several visuals appear on more than one page (for example the KPI cards, top 5 machines, and rejection trend). That is fine for navigation, but keep them in sync when editing.
- **"Rejection by Employee" chart.** It uses Produced Qty rather than Rejected Qty, which looks like a mismatch with its title.
- **Wastage % has two definitions** (divided by processed vs divided by manufactured minus rejected). Pick one for reporting.
- **Missing values.** Some columns are mostly empty (for example `Sapgrno`, `Final Processed Qty`, and the employee machine fields), so avoid relying on them.
