# Kayao Purchasing Dashboard (`index.html`)

A one-file dashboard showing purchasing, cost ratios and accounts payable for **Kayao**, 1252 N Wells St, Chicago. It is built from the workbook `Kayao_Invoices_September_2026.xlsx`. It uses Akiro's layout and colors, but contains Kayao data only.

## Opening it

Double-click `index.html` to open it in any modern browser. There is nothing to install and no server to run.

- **Offline:** everything works. The only outside request is for the Google Fonts (Shippori Mincho, Zen Kaku Gothic New), and without them the page falls back to system fonts.
- **Light / dark mode:** the ☾ button in the header switches themes. Your choice is remembered in that browser (key `kayao-theme`).
- The page fits phone width, so it can be opened on a phone too.

## What's on each tab

| Tab | What it shows |
|---|---|
| **Overview** | Still owed and how much is past due; product spend in the latest full week vs the week before; Food and Bar cost ratios for the latest week vs the average; August money carried into later months; the product-spend-by-week chart (tap a week to open it); the invoices needing attention; and the cost-ratio chart. |
| **Weeks** | One week at a time: product by category (Food, Bar, Supply, Cleaning), bar sub-categories, freight/fees/tax, invoice count, and ratios when sales are loaded. Month totals for August, September and October. |
| **Owed** | Every NOT PAID invoice, grouped as **past due**, **due within 7 days** and **later / no due date**. Day counts are measured from the "Updated" date, not today's date. |
| **Invoices** | All invoices in the file, searchable and sortable, with date, week, product, fees, total, status, paid date, due date and the payment note from the workbook. |
| **Vendors** | Product spend by vendor, with a weekly sparkline (A1 to O5) and share of total. |
| **Watch list** | Open issues to act on, tagged **Billing**, **Price** or **Open** (credits owed, missing invoices, price swings and similar). |

## How the numbers are defined

These follow the workbook exactly.

- **Weeks** run Monday to Sunday, by **invoice date**. A week that crosses a month end goes entirely to the month with more of its days. So:
  - August = A1–A5 (A1 = 07/27–08/02 stays in August by choice, even though it is mostly July)
  - September = S1–S4 (08/31–09/27)
  - October = O1–O5 (O1 = 09/28–10/04, O5 = 10/26–11/01)
- **Product** is goods only. Freight, fuel, delivery and service fees, deposits, sales tax and alcohol taxes are shown separately as fees, never as product cost.
- **Cost ratios** are purchases ÷ Toast ProductMix net sales. Food purchases are compared with food sales, and Bar purchases with drink sales (cocktails & liquor, beer, wine, non-alcoholic). Month and total ratios count only weeks that have sales loaded.
- **Partial weeks** are labelled "part week". Right now only A1 is partial, because its July invoices (07/27–07/31) aren't loaded.
- **PAID / NOT PAID** comes from the Invoices tab. An invoice is marked PAID only with evidence, and the source is recorded in its note.

## Updating it

The page has two parts:

1. **The data**, in a single block that starts with `window.KAYAO_DATA = {` near the top of the file.
2. **The app code** below it, which draws everything from that block.

After every change to the workbook, **replace only the data block** and leave the app code alone. Then open the page and check that it loads without errors.

The data block holds:

| Key | Contents |
|---|---|
| `asOf` | The "Updated" date (YYYY-MM-DD). Due-date counts are measured from it. |
| `weeks` | One entry per week: `id`, `start`, `end`, `month`, `entered`, `invoices`. |
| `categories` | Weekly product by Food, Bar, Supply, Cleaning (one number per week, same order as `weeks`). |
| `barSub` | Weekly bar purchases by Cocktails & liquor, Beer, Wine, Non-alcoholic. |
| `fees` | Weekly freight, fees and tax. |
| `vendors` | Each vendor's name and weekly product spend, sorted largest first. |
| `sales` | Weekly Toast net sales by category, plus `TOTAL NET SALES`. Use 0 for weeks not loaded. |
| `partial` / `partialNote` | Which weeks are marked partial, and why. |
| `invoices` | One entry per invoice: vendor, number, date, week, product, fees, total, status (`paid` / `open`), paidOn, due, note. |
| `watch` | Watch-list items: `tag`, `title`, `body`. |

Every weekly list must have the same number of entries as `weeks` and be in the same order.

## Check before sharing

- Invoice count and the paid / owed totals match the Invoices tab (Payment Status block).
- Weekly product matches the Summary tab.
- The page opens with no errors, and every tab loads.
