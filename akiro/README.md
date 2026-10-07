# Akiro Chicago purchasing dashboard

A one-page dashboard for Akiro Chicago (546 N Wells St). It shows what we bought each week, what it cost against sales, what we still owe, and which vendors and prices to watch.

Everything lives in a single file, `index.html`: design, logic and data. No installs, no build step and no other files are needed.

## What's inside

| Tab | What it shows |
|---|---|
| **Overview** | Total still owed, last week's spend compared with the week before, food and bar ratios, the August carry-over, product spend by week, invoices that need attention, and the ratio trend |
| **Weeks** | Any fiscal week (A1–A4, S1–S5) or a whole month: spend, fees, food and bar ratios, category and bar mix, vendors, and every invoice in the period |
| **Owed** | Unpaid invoices, filtered by past due, next 7 days or later, and by vendor. Includes the August carry-over. Downloadable as CSV |
| **Invoices** | Every invoice, with search and filters for month, status and vendor. Tap a row to see its notes. Downloadable as CSV |
| **Vendors** | Spend to date, share of total, a weekly trend and the amount owed. Tap a vendor for its full invoice history |
| **Watch list** | Billing issues, price changes and open questions |

It works on phones, has light and dark modes, and can be used with a keyboard (← and → step through weeks).

## Viewing it

Download `index.html` and open it in any browser (Chrome, Safari, Edge or Firefox).

## Publishing on GitHub Pages

1. Create a repository and upload `index.html` (and this README) to its root.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, branch `main`, folder `/ (root)`, then **Save**.
4. After a minute or two the dashboard is live at `https://<your-username>.github.io/<repository-name>/`.

> The file must be named `index.html` for GitHub Pages to open it automatically. If you keep another name, add it to the end of the address, for example `.../akiro-dashboard.html`.

## Weekly updates

1. Send the week's invoices, payment statements and POS ProductMix sales report to be entered in the purchasing workbook.
2. You receive a new `index.html` with the updated numbers.
3. In the repository, choose **Add file → Upload files** and upload the new file with the same name. Commit the change.
4. GitHub Pages redeploys automatically. Refresh the page after a minute or two.

The top-right corner of the dashboard shows the "Updated" date and the last week included, so you can confirm the new version is live.

## Privacy

A GitHub Pages site is **public to anyone who has the link**, even if the repository itself is private (unless you are on GitHub Enterprise with private Pages).

This dashboard contains vendor names, invoice amounts and sales figures. If those should stay private, either:

- keep the repository private and open `index.html` from your computer, or
- host it somewhere that requires a login.

## How to read the numbers

- **Fiscal weeks** run Monday to Sunday. August is A1–A4 (08/03–08/30); September is S1–S5 (08/31–10/04).
- **"August" invoices** are those dated 08/01 to 08/30. Invoices dated 08/31 fall in S1 and count as September.
- **Ratios** are purchases divided by net sales for the same days. They are purchase ratios, not food cost: stock on hand is not counted.
- **Past due** is measured from the dashboard's "Updated" date, not today's date.
- **Paid** means payment was confirmed by a statement, a payment portal, a paid stamp on the invoice, or the owner. A past due date alone never marks an invoice paid.
