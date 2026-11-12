# Member Finance Dashboard (Power BI)

A five-page Power BI report that explains an organization's finances to its members in plain language: where dues go, whether spending matches the approved budget, how membership is trending, how secure the reserves are, and how much money flows back to local chapters.

> **All numbers in this project are fictional sample data.** They were generated to look realistic and to tie out, so the report can be designed and tested. They do not represent any real organization's finances.

**[View the full report as a PDF](Member_Dashboard.pdf)**

## What the report answers

| Page | Member question |
| --- | --- |
| 1. Where Your Dues Go | Of every dollar spent, how much goes to staff, organizing, chapters, and overhead? |
| 2. Budget vs. Actual | Is spending what was approved? Which lines are over or under? |
| 3. Dues & Membership | Is the dues base growing? Who joined and who lapsed? |
| 4. Cash & Reserves | Could the organization weather a sudden drop in dues? |
| 5. Chapter Dues Share | How much money goes back to chapters, and to which regions? |

Every page shares a header, a page navigator, a Year slicer, and a "Figures through [month]" label that updates on refresh.

## Repository contents

| File | What it is |
| --- | --- |
| `Member_Dashboard.pbip` (+ folders) | The Power BI project, saved as text so changes are visible in Git |
| `Member_Finance_Sample_Model.xlsx` | The sample data, as 10 Excel tables |
| `Member_Dashboard.pdf` | A PDF export of all five pages |
| `README.md` | This file |

## Data model

A star schema: four dimension tables filter five fact tables, all joined on a month.

**Dimensions**
- `DimDate`: one row per day, marked as the date table
- `DimAccount`: chart of accounts, with a member-friendly `PlainLabel` for each line
- `DimDepartment`: departments, with plain-language descriptions
- `DimChapter`: chapters, with state, region, and size band

**Facts**
- `FactBudget`: monthly budget by account and department
- `FactActual`: monthly actuals by account and department
- `FactDues`: monthly paying members, new, lapsed, and dues revenue by dues type (aggregate counts only, no member records)
- `FactCash`: month-end balances by fund (Operating, Board-Designated Reserve, Restricted)
- `FactChapterDuesShare`: quarterly dues share and grants paid to each chapter

**Parameters**
- `Settings`: one row holding `LastClosedMonth`, `ReserveTargetMonths`, and `DuesShareRate`

**Relationships (10, all many-to-one, single direction)**
- `DimDate[Date]` to `MonthStart` in FactBudget, FactActual, FactDues, FactCash, and to `FactChapterDuesShare[PaymentMonth]`
- `DimAccount[AccountID]` to FactBudget and FactActual
- `DimDepartment[DeptID]` to FactBudget and FactActual
- `DimChapter[ChapterID]` to FactChapterDuesShare

## Measures

Measures are grouped into display tables by purpose:

| Table | Contents |
| --- | --- |
| `_Measures_Core` | Base actual and budget amounts, revenue and expense splits, last closed month |
| `_Measures_YTD` | Year-to-date actuals and budgets, limited to closed months so they compare fairly |
| `_Measures_Bud_vs_Act` | Variance (positive always means better than plan), percent of budget, line status |
| `_Measures_Dues_Go` | Cents per dollar, share of spending, spending per member |
| `_Measures_Dues` | Paying members, year-over-year growth, joined and lapsed, average dues, lapse rate |
| `_Measures_Cash` | Cash by fund, trailing 12-month spending, months of reserves, reserve target and gap |
| `_Measures_Chapters` | Dues share, grants, per-member share, share of dues returned |
| `_Measures_Labels` | Dynamic card titles, labels, notes, and status colors |

Design choices worth noting:
- **Cents per dollar** instead of percentages on the main page, because it reads faster for non-accountants.
- **One variance measure for every row**: positive means more revenue or less spending than planned.
- **Snapshot measures** (members, cash) take the last month in context instead of summing.
- **Labels are measures**, so text like "January to October" updates when the data does.

## How to use it

1. Open `Member_Dashboard.pbip` in Power BI Desktop.
2. If prompted, point the data source to your copy of `Member_Finance_Sample_Model.xlsx` (Transform data > Data source settings > Change source).
3. Refresh.

### Monthly update

1. Add the new month's rows to each fact table in the Excel file.
2. Change `LastClosedMonth` in the `Settings` table.
3. Refresh. All "January to [month]" labels, YTD figures, and reserve calculations update on their own.

### Switching to real data

Replace the rows in each table but keep the column names the same. Before sharing, check these tie-outs:

| Check | Should equal |
| --- | --- |
| Membership dues in FactActual | DuesRevenue in FactDues, by month |
| Chapter dues share in FactActual | DuesShareAmount in FactChapterDuesShare, by payment month |
| Chapter grants in FactActual | GrantAmount in FactChapterDuesShare, by payment month |
| Monthly change in Operating + Reserve cash | Revenue minus expenses for the month |

## Privacy rules for a member-facing version

- No individual member data. Counts only.
- No individual staff pay. Staff shown as one line.
- No vendors or payees.
- Chapter money shown by state and region only.
- Never commit real financial data or member information to this repository.

## Built with

- Power BI Desktop
- Excel (sample data)

## About Me

I'm Michelle Leonard, a finance and data professional with 20+ years of experience turning messy multi-system data into reporting that leaders trust and use.

- **LinkedIn:** [linkedin.com/in/michelle-leonard-331126b](https://www.linkedin.com/in/michelle-leonard-331126b)

### More of my work

- [Higher Ed Financial Dashboard](https://github.com/michelleleonard23/higher-ed-financial-dashboard)
- [Overtime Dashboard](https://github.com/michelleleonard23/overtime-dashboard)
