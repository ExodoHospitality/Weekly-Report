# Matilda purchasing dashboard (`index.html`)

A single-file web dashboard that shows weekly purchasing, cost ratios and accounts payable for **Matilda** (Matilda and Bar Clandestino LLC, 535 N Wells St, Chicago 60654).

The page is built entirely from the master workbook, `Matilda_Invoices_September_2026.xlsx`. It has no backend, needs no build step and has no JavaScript dependencies. To use it, open `index.html` in a browser, or host it as the index page of any static site.

> Previously named `Matilda_dashboard.html`. The file only changed its name; the code and data are identical.

---

## Contents

1. [What it shows](#what-it-shows)
2. [File structure](#file-structure)
3. [The data block (`window.MATILDA_DATA`)](#the-data-block-windowmatilda_data)
4. [How the figures are calculated](#how-the-figures-are-calculated)
5. [Updating the dashboard](#updating-the-dashboard)
6. [Navigation and URLs](#navigation-and-urls)
7. [Styling and theming](#styling-and-theming)
8. [Accessibility, mobile and print](#accessibility-mobile-and-print)
9. [Known quirks](#known-quirks)
10. [Position at the last build](#position-at-the-last-build)

---

## What it shows

The page has six tabs.

| Tab | Shows |
|---|---|
| **Overview** | KPI tiles: still owed, last full week's spend, food and bar ratios, and August invoices carried into September. Also a 14-week spend chart split by category, a "Needs attention" list (past due plus due within 7 days, up to 7 items), and a line chart of the food and bar ratios. |
| **Weeks** | One week (A1 to O5) or one month. Shows product spend, fees and tax, and the food and bar ratios with change against the previous week. Below that: spend by category, bar mix, spend by vendor, and the period's invoice list. The ‹ › buttons and the ← → arrow keys step between weeks. |
| **Owed** | Every NOT PAID invoice, split into past due, due in the next 7 days and due later or no due date. Filters by status and vendor, with a CSV export. Also has the "August carried into September" reconciliation, listing what was paid in September and what is still open. |
| **Invoices** | The full register. Search by invoice number, vendor or note, and filter by month, status or vendor. Columns sort, a row expands to show its note, and the list exports to CSV. |
| **Vendors** | One row per vendor: a 14-week sparkline, spend to date, share of total spend, invoice count and amount owed. Clicking a vendor opens a side drawer with its weekly spend and invoices. |
| **Watch list** | Watch items from the workbook's Summary tab, grouped as Billing issues, Price changes or Open questions. |

Partial weeks (currently A1, O1 and O2) are drawn hatched and marked "part week". Their tooltip and title show the reason from `partialNote`.

---

## File structure

`index.html` is self-contained. From top to bottom it has these parts.

```
<head>
  meta / title / favicon (inline SVG emoji)
  Google Fonts: Shippori Mincho (headings) + Zen Kaku Gothic New (body)
  <style>   design tokens (:root), light/dark themes, layout, components, print rules
<body>
  header.bar     brand, "Updated …" stamp, theme toggle, tab nav
  main#main      six <section class="view"> blocks, one per tab
  dialog#drawer  vendor detail drawer
  div#tip        floating tooltip
  footer.foot    calendar explanation
  <script>       DATA BLOCK: window.MATILDA_DATA = {...};   ← generated, replace on every update
  <script>       APP CODE (IIFE, "use strict") ← do not change when updating data
```

### App code map (second `<script>`)

| Section | Purpose |
|---|---|
| helpers | `$`, `$$`, `esc` (HTML escaping), `sum`, `money` and `money2` (USD formatting), `pct`, `md` (ISO date to MM/DD), and `daysTo` (days from `asOf`). |
| constants | `CATS` (Food, Bar, Supply, Cleaning), `SUBS` (bar subcategories), the colour maps, and the `entered`, `complete` and `partial` week indexes. `lastIdx` is the latest *complete* week. |
| invoice prep | Copies `D.invoices` and adds `month`. Open invoices also get a `bucket` (`past`, `soon` or `later`) and readable `dueText`. |
| theme | Light/dark toggle. The choice is saved in `localStorage` under `matilda-theme`, and the code fails silently if storage is blocked. |
| `table()` | Generic sortable table with keyboard-clickable rows, optional expandable note rows and a footer. |
| `csv()` | Builds a CSV in the browser and downloads it. |
| `ratiosFor()` | Purchase ÷ sales ratios. It uses only weeks that have sales typed in, which is the same rule as the workbook. |
| `renderOverview`, `ratioChart` | Overview tab. The chart is hand-built SVG. |
| `renderWeeks`, `step` | Weeks tab and week stepping. |
| `carryData`, `renderOwed` | Owed tab and the August carry-over. |
| `renderInvoices` | Invoices tab. |
| `spark`, `renderVendors`, `openVendor` | Vendors tab and drawer. |
| `renderWatch` | Watch list. |
| router | Hash routes (`#overview`, `#weeks/S3`, and so on), handled in JavaScript so they also work in sandboxed previews. |

---

## The data block (`window.MATILDA_DATA`)

The page reads all of its numbers from one JSON object in the first `<script>` tag:

```html
<script>
// Generated from Matilda_Invoices_September_2026.xlsx - do not edit by hand.
window.MATILDA_DATA = { ... };
</script>
```

If the object is missing or fails to parse, the page shows "The data block did not load" in place of its content.

### Schema

All week arrays have **14 positions**, in this order: `A1 A2 A3 A4 A5 S1 S2 S3 S4 O1 O2 O3 O4 O5`.

| Key | Type | Contents |
|---|---|---|
| `asOf` | `"YYYY-MM-DD"` | Position date. Due buckets ("past due", "due in N days") are calculated against this date, not today's date. |
| `weeks` | array of 14 | `{ id, start: "MM/DD", end: "MM/DD", month: "August" \| "September" \| "October", entered: bool, invoices: int }`. When `entered` is false the week shows as "not in yet". |
| `categories` | object | `{ Food: [14], Bar: [14], Supply: [14], Cleaning: [14] }`: product $ by week, from Line Items. |
| `barSub` | object | `{ "Cocktails & liquor": [14], Beer: [14], Wine: [14], "Non-alcoholic": [14] }`, from Line Items `Bar Subcategory`. |
| `fees` | array of 14 | Freight, fees and tax by week (Invoices `Total Fees`). |
| `vendors` | array | `{ name, weeks: [14] }`: product $ by vendor by week. There are currently 23 vendors. |
| `partial` | array | IDs of partial weeks, e.g. `["A1","O1","O2"]`. |
| `partialNote` | object | `{ weekId: "reason shown in tooltip and title" }`. |
| `sales` | object | `{ Food, "Cocktails & liquor", Beer, Wine, "Non-alcoholic", "TOTAL NET SALES" }`, each `[14]`. Toast net sales from the blue rows on the Ratios tab. Use `0` for weeks with no sales yet. |
| `invoices` | array | One object per document; see below. |
| `watch` | array | `{ tag: "Billing" \| "Price" \| "Open", title, body }`, newest first. |

#### Invoice object

```json
{
  "vendor": "Allen Brothers",
  "number": "293710 (Chefs Whse)",
  "date": "2026-07-31",
  "week": "A1",
  "product": 1501.58,
  "fees": 7.95,
  "total": 1509.53,
  "status": "paid",
  "paidOn": "2026-08-31",
  "due": "2026-08-07",
  "note": "…"
}
```

- `status` is lower-case `"paid"` or `"open"`. These map to the workbook's **PAID** and **NOT PAID**.
- `fees` is the workbook's *Total Fees*, which is freight and fees plus sales tax.
- `paidOn` and `due` are ISO dates or `null`. An open invoice with no `due` goes in the "later" bucket.
- `week` must be one of the 14 IDs. Credits are entered as negative `product` and `total`.

### Where each field comes from in the workbook

| JSON | Workbook source |
|---|---|
| `categories`, `barSub`, `vendors[].weeks` | **Line Items**, rows 6–2500: `Extended Price` summed by `Week` and by `Category`, `Bar Subcategory` or `Vendor`. These should equal **Summary** columns B–O. |
| `fees`, `invoices`, `weeks[].invoices` | **Invoices**, rows 5–400. |
| `sales` | **Ratios**, the blue rows 8, 13, 17, 21 and 25, columns B–O. |
| `watch` | **Summary**, WATCH ITEMS block. |
| `partial`, `partialNote` | Data Completeness on the Summary tab and the notes on the Ratios tab. |

---

## How the figures are calculated

- **Product spend** = the sum of the four categories for the selected weeks.
- **Food ratio** = Food purchases ÷ Food net sales.
- **Bar ratio** = Bar purchases ÷ (Cocktails & liquor + Beer + Wine + Non-alcoholic sales).
- **Month and average ratios** include only weeks with sales typed in (`sales.Food[i] > 0`). A week with invoices but no sales therefore does not inflate the ratio. This is the same rule as the Ratios tab.
- **Ratio flags** on the Weeks tab: "high for us" means more than 125% of the all-weeks average, and "unusually low" means less than 60% of it.
- **Due buckets** are counted from `asOf`. `past` is a due date before `asOf`, `soon` is due within 0–7 days, and `later` is more than 7 days away or has no due date.
- **August carried into September** covers invoices dated 08/01–08/30 that were either paid on or after 09/01, or are still open.
- **Last full week** (header and Overview KPIs) is the latest entered week that is not marked partial.

These are **purchase ratios, not cost of goods sold**: stock on hand is not counted. The page says this on the Weeks tab.

---

## Updating the dashboard

The rule is: **replace only the data block. Never change the layout, styles or app script.**

1. Update the workbook. Recalculate it, confirm there are zero formula errors and that every Payments tie-out says "ties".
2. Rebuild the `MATILDA_DATA` JSON from the workbook, using the schema above.
3. In `index.html`, replace everything between `window.MATILDA_DATA = ` and the `;` just before `</script>`.
4. Set `asOf` to the new position date, and update `weeks[].entered`, `partial` and `partialNote`.
5. Check the result:
   - The sum of `invoices[].total` equals the Invoices tab total. At the last build that was **$192,403.93**.
   - The paid and open sums equal the PAID and NOT PAID totals.
   - The category sums equal the Summary PRODUCT TOTAL.
   - The file contains **no text from other restaurants**.
   - The page opens without the "data block did not load" message.

### Example rebuild script (Python)

```python
import json, re, pathlib
html = pathlib.Path("index.html").read_text(encoding="utf-8")
data = build_matilda_data("Matilda_Invoices_September_2026.xlsx")   # your workbook reader
block = "window.MATILDA_DATA = " + json.dumps(data, ensure_ascii=False, separators=(",", ":")) + ";"
html, n = re.subn(r"window\.MATILDA_DATA\s*=\s*\{.*?\};(?=\s*</script>)", lambda m: block, html, flags=re.S)
assert n == 1, "data block not found"
pathlib.Path("index.html").write_text(html, encoding="utf-8")
```

### Adding a vendor

Append `{ name, weeks: [14] }` to `vendors`. The Vendors tab, the drawer and the filter dropdowns all pick it up automatically.

### Adding weeks beyond O5

The code assumes 14 weeks in three months (August, September, October). To add November, you would need to:

- extend every week array;
- add the new month to the month loop in `renderOverview` and to `SCOPES`;
- add the month to the `month` mapping in the invoice prep, and to the Month `<select>` on the Invoices tab;
- raise `min-width` and `repeat(14, …)` on `.ledger` in the CSS.

---

## Navigation and URLs

| URL hash | Opens |
|---|---|
| `#overview` (default) | Overview |
| `#weeks` | Weeks, on the last full week |
| `#weeks/S3` | Weeks, on S3 (any week ID) |
| `#weeks/aug`, `#weeks/sep`, `#weeks/oct` | Weeks, whole month |
| `#owed`, `#invoices`, `#vendors`, `#watch` | That tab |

Back and Forward work through `pushState` and `popstate`. On the Weeks tab, ← → step between weeks unless the cursor is in a form field.

---

## Styling and theming

- All colours are CSS custom properties on `:root`.
- Dark mode follows the system setting, unless the user has chosen otherwise with the ☾/☀ button (stored in `data-theme`).
- Category colours: Food is indigo, Bar is amber, Supply is stone and Cleaning is mist.
- Bar subcategory colours: liquor, beer, wine and N/A (`--sake` is defined but not used).
- Status colours: past due is `--stamp` (red), due soon is `--warn` (amber) and paid is `--moss` (green).
- Fonts load from Google Fonts. Without a connection the page falls back to system serif and sans-serif fonts and still works.

---

## Accessibility, mobile and print

- There is a skip link, `aria-current` on the active tab, `aria-pressed` on toggles, `aria-sort` on sortable headers, and `aria-live` on the invoice count.
- Rows open with Enter or Space, the drawer is a native `<dialog>`, and tooltips also appear on keyboard focus.
- Animation is turned off when the user has asked for reduced motion.
- Breakpoints:
  - At 860px and below, the two-column grids stack and the "Updated" stamp is hidden.
  - At 560px and below, KPIs show two per row, secondary table columns are hidden, and the week chart scrolls sideways.
- In print, the header, filters and buttons are hidden, all views are shown, and cards are kept from splitting across pages.

---

## Known quirks

- **The Vendors tab sparkline header says "A1 to S5".** It should say "A1 to O5". This is a cosmetic label only; the sparklines do show all 14 weeks. The fix is in `renderVendors`, `label: "A1 to S5"`.
- **"Due in the next 7 days" on the dashboard is not the same as "due by 10/31" in the workbook.** The Payments tab buckets run to month-end, while the dashboard counts 7 days from `asOf`. Both add up to the same NOT PAID total.
- **The "Carried from August" KPI covers August only.** It does not yet show September carried into October.
- **The source filename is hard-coded in two places:** the data-block comment and the footer text. If the workbook is renamed, update both.

---

## Position at the last build

Data `asOf` is **2026-10-06**.

**Volume:** 326 documents, 23 vendors.

**Totals:**
- Product: $186,915.58
- Fees and tax: $5,488.35
- Invoice total: $192,403.93

**Payment status:**
- Paid: 275 invoices, $166,721.97
- Not paid: 51 invoices, $25,681.96

**Weeks:** A1–O1 entered, O2 has one invoice so far, O3–O5 are empty. Partial weeks are A1, O1 and O2.
