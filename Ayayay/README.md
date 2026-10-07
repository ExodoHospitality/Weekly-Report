# Ayayay purchasing dashboard — `index.html`

A single-file web page that shows purchasing, cost ratios and accounts payable for **Ayayay** (EXODO HOSPITALITY LLC dba Ayayay, 60 E Lake St, Chicago).

- **Built from:** `Ayayay_Invoices_October_2026.xlsx`
- **Position as of:** 10/06/2026
- **Covers:** August, September and October 2026, weeks A1–O5

## Opening it

Double-click `index.html` and it opens in any modern browser (Chrome, Edge, Safari, Firefox).

- It needs no install, server or internet connection. The only thing it loads from the web is two Google fonts; if those can't load, the page falls back to system fonts.
- The data is stored inside the file, so the page never changes on its own. Rebuild it from the workbook to update it (see below).
- To host it, upload `index.html` to any static web host or shared folder. Because it's named `index.html`, it opens as the default page of that folder.

## What's on each tab

| Tab | What it shows |
|---|---|
| **Overview** | Total still owed (and how much is past due), product spend for the latest week, food and bar ratios for that week against the average, and what August carried into September. Below that: a weekly spend chart split into Food, Bar, Supply and Cleaning; a list of invoices past due or due in the next 7 days; and a line chart of the cost ratios. |
| **Weeks** | Pick any week (A1–O5) or a whole month. Shows product spend, fees and tax, food and bar ratios, where the money went by category, the bar mix, spend by vendor, and every invoice in that period. The ‹ › buttons and arrow keys move one week at a time. |
| **Owed** | Every unpaid invoice, filtered into past due, due in the next 7 days, or due later, and by vendor. Also shows the August invoices that were paid in September or are still open. Has a **Download CSV** button. |
| **Invoices** | All invoices, searchable by number, vendor or note, with filters for month, PAID / NOT PAID and vendor. Click a row to read its note (terms, fees, payment reference). Has a **Download CSV** button. |
| **Vendors** | Spend by vendor to date, each vendor's share of the total, a small weekly spend chart and the amount owed. Click a vendor for its invoices. |
| **Watch list** | Short cards grouped into billing issues, price changes and open questions. |

The ☾ / ☀ button switches between light and dark themes.

## How the numbers are defined

- **Weeks** run Monday to Sunday and are assigned by **invoice date**. A week that crosses a month end goes to the month with more of its days.

  | Month | Weeks | Notes |
  |---|---|---|
  | August | A1–A5 | A1 = 07/27–08/02, but only 08/01–08/02 are loaded, so it's marked "part week". |
  | September | S1–S4 | 08/31–09/27 |
  | October | O1–O5 | O1 = 09/28–10/04 |

- **Product spend** is product cost only. Freight, fuel surcharges, service charges and taxes are shown separately as "Fees & tax".
- **Ratios** are purchases divided by net sales for the same days:
  - **Food ratio** = food purchases ÷ food sales.
  - **Bar ratio** = bar purchases ÷ drink sales (cocktails & liquor, beer, soda and open drink).
  - Catering and Events sales are left out.
  - Weeks without sales show "–". Averages use only weeks that have sales.
  - These are purchase ratios, not true food cost, because stock on hand isn't counted.
- **Paid / not paid** comes straight from the workbook's Status column. An invoice counts as PAID only with evidence: a register or card receipt, a vendor payment report (US Foods EFT, SyscoPay, Fintech, the Allen Brothers statement) or the owner's written confirmation of the month paid. Everything else is NOT PAID.
- **Past due / due soon** is worked out from each invoice's due date against the as-of date (10/06/2026). El Popocatepetl tickets print no terms, so they have no due date.

## Updating it

The page is generated from the workbook, so don't edit the numbers in the HTML by hand.

1. Add the new invoices, payments and sales to `Ayayay_Invoices_October_2026.xlsx`.
2. Rebuild `index.html` from the updated workbook. Ask Claude to "rebuild the index" with the new workbook.
3. Replace the old `index.html` wherever it's stored or hosted.

When rebuilt, the dashboard always matches these figures in the workbook:

| Workbook location | Must equal |
|---|---|
| Invoices tab, I302 | Total billed |
| Invoices tab, P304 | Total NOT PAID |
| Summary tab, S10 | Total product spend |
| Ratios tab | Weekly sales and ratios |

## Inside the file (for whoever maintains it)

- **Format:** one HTML file with no external scripts or libraries.
- **Data:** all figures are in a single block near the top, `window.AYAYAY_DATA = { … }`. It contains:

  | Key | What it holds |
  |---|---|
  | `asOf` | The position date |
  | `weeks` | 14 weeks with dates, month, invoice count and whether each is loaded |
  | `categories` | Food / Bar / Supply / Cleaning spend per week |
  | `barSub` | Bar purchases by type per week |
  | `fees` | Fees & tax per week |
  | `vendors` | Spend per vendor per week |
  | `sales` | Net sales per category per week |
  | `invoices` | One record per invoice: vendor, number, date, week, product, fees, total, status, paid-on date, due date, note |
  | `watch` | The watch-list cards |
  | `partial`, `partialNote` | Which weeks are only partly loaded |

- **Code:** the page logic follows the data block and reads only `window.AYAYAY_DATA`.
- **Browser storage:** only the theme choice is saved, under the key `ayayay-theme`.

## Status as of 10/06/2026

| | |
|---|---|
| Invoices | 140 |
| Billed | $57,980.91 |
| Paid | $41,388.74 |
| Not paid | $16,592.17 |
| Past due (4 invoices) | $2,481.27 |
| Weeks loaded | A1–O1, with O1 sales in |
| Weeks not loaded yet | O2–O5 |
