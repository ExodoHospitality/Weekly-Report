Exodo Hospitality — Weekly Performance Ledger

Internal KPI dashboard for the four Exodo Hospitality venues. A single self-contained HTML page: weekly and monthly sales, cost and labor figures measured against house benchmarks, with vendor-level drill-downs on food and bar cost.

Live site: https://exodohospitality.github.io/Weekly-Report Access code: Exodo2026 Data currently published: through the week ending 2026-10-04

Locations covered
Location	Weeks logged	History begins
Akiro Hand Roll Bar	54	2025-09-22
Kayao Restaurant	66	2025-06-30
Matilda | Clandestino	66	2025-06-30
Ayayay Mexican Eatery	66	2025-06-30

Matilda and Clandestino share a P&L but are tracked separately for labor, so that location shows split BOH, FOH and salaried percentages — Matilda labor measured against Matilda sales, Clandestino against Clandestino sales.

What's on the page

Sales overview — gross, net, and the food/bar split, averaged across whatever period is selected.

Benchmark scorecard — each KPI against its house target, colored green or red. Food COGS and Bar COGS carry a Detail badge and open a drill-down (see below).

Trend charts — gross vs. net sales, food/bar mix, dinner vs. brunch average check, then every cost and labor KPI plotted against its benchmark line.

Raw ledger — the full weekly table, collapsed by default.

Benchmarks
KPI	Target
Discount %	≤ 2%
Food COGS	≤ 25%
Bar COGS	≤ 18%
FOH labor	≤ 8%
BOH labor	≤ 12%
Salaried	≤ 10%
Prime cost	≤ 55%
Controls
Location — switches venue.
Weekly / Monthly — monthly figures are rolled up from the weeks currently in range, so a partial month reports only the weeks you selected, never a full month.
From week / To week — lists the actual weeks present in the workbook. The two stay ordered; picking the same week in both shows a single week. All weeks resets.
Update from Excel — see below.
Food and Bar COGS drill-downs

Clicking either card opens a popup built from the per-vendor detail tabs:

Food — purchases by vendor (Sysco, Fortune Fish, Allen Brothers, and so on), as a stacked weekly chart plus a share-of-spend table.
Bar — the bar tabs carry sales and purchases per category, so this popup also shows COGS % per category (Cocktails/Liquor, Beer, Wine, Non-Alcoholic, plus Sake at Akiro).

The badge only appears when detail rows exist for the selected weeks, so it hides for Ayayay and for date ranges outside the itemized period.

Updating the data

The page reads the workbook in your browser. Nothing is uploaded anywhere, and the file is never written back to.

Looking at new numbers yourself
Open the dashboard, enter the access code.
Click Update from Excel and pick Exodo weekly sales log.xlsx.
Everything redraws — cards, charts, monthly rollups, ledger, popups.

The status line under the heading confirms what loaded.

This is view-only and temporary. Refreshing the page reverts to the data baked into the file, and nobody else sees your upload. To change what everyone sees, the file in this repository has to be replaced.

Publishing an update for everyone

Replace index.html in this repository with a rebuilt copy, then commit. GitHub Pages redeploys in about a minute.

After publishing, confirm it took: open the site and read the small grey line under the "Exodo Hospitality" heading. It states the data date. If it still shows the old one, your browser is serving a cached copy — open the URL with ?v=2 on the end (bump the number each time), or hard refresh with Ctrl+Shift+R / Cmd+Shift+R.

How the workbook is read

The parser matches column headers by name, not position, so columns can be inserted or reordered safely. Renaming a header silently drops that field to zero.

Tabs read:

Tab	Used for
Akiro Hand Roll Bar, Kayao Restaurant, Matilda & Clandestino, Ayayay Mexican Eatery	all weekly KPIs
Akiro Food, Kayao Food, Matilda Food	food vendor drill-down
Akiro Bar, Kayao Bar, Matilda Bar	bar category drill-down

Hoja 11, Payrolls W2 and Bonus are ignored. New vendor columns are picked up automatically. Adding Ayayay Food / Ayayay Bar tabs in the same shape would give Ayayay drill-downs with no code change.

Monthly figures are always computed, never read from the sheet — weekly rows are bucketed by the start date's month. Percentages use the correct denominators: COGS over its own sales line, labor over net sales, Matilda/Clandestino splits over their respective sales.

Known data caveats

Detail tabs lag the main tabs. The six vendor tabs currently cover 8 weeks ending 2026-09-27, while the main tabs run to 2026-10-04. Nothing breaks — the popups label their own range — but the most recent weeks have no vendor breakdown.

Detail totals don't reconcile with the main tabs. Vendor purchases and the main tab's Food cost / Bar cost disagree for the same weeks. Food has converged to roughly 1–3%, but bar still runs 5–15% apart. The popups state the gap whenever it appears. The KPI cards and charts always use the main tab figure. The persistence of the bar gap suggests a supplier or category missing from the bar detail tabs rather than invoice timing — worth resolving at the source.

Ayayay has no detail tabs, so its COGS cards have no drill-down.

Security

The access code is not real protection. It is a string compared in JavaScript, in a file served publicly. Anyone who opens the page source can read both the code and every figure without ever seeing the prompt. It deters a glance over the shoulder, nothing more.

GitHub Pages cannot be made private on the free plan — Pages serves from public repositories on GitHub Free, and genuinely private publication requires GitHub Enterprise Cloud. If this data needs actual access control, host it behind an authentication gateway instead; Cloudflare Pages with a Cloudflare Access policy does this on a free tier and emails a one-time code to addresses you approve.

Treat the published URL as public, and share it accordingly.

Technical notes

Single file, no build step, no dependencies to install. Two libraries load from CDN: Chart.js 4.4.1 for the charts, SheetJS 0.18.5 for reading workbooks in the browser. Fonts come from Google Fonts. All data is embedded in the page as a JSON object.

The layout works on a phone — cards drop to two columns and charts stack — but the 11-column ledger table needs horizontal scrolling. It's built for a desktop screen.
