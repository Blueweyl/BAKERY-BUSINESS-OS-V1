# Bakery Business OS

Run a whole bakery from one file. `index.html` is the entire product: open it in
any browser and it works — no install, no account, no server, no internet.

## What it does

Nine modules, all sharing one set of records:

| Module | What it covers |
| --- | --- |
| **Dashboard** | Revenue, average order value, net profit and money owed, with a "today's priority" feed of expiring stock, overdue balances and unconfirmed quotes |
| **Order calendar** | Every order laid out by day, colour-coded by stage, month by month |
| **Enquiries & orders** | Enquiry → quoted → booked → paid, as a drag-and-drop board plus a searchable table |
| **Customers** | Contact details, notes, order history and lifetime spend, calculated automatically |
| **Products & costing** | A recipe editor priced from live inventory, the pricing formula, profit per unit and the allergen matrix |
| **Inventory** | Stock levels, reorder points, expiry warnings, stock value and a shopping list |
| **Payments & P&L** | Received, outstanding and expected money, expenses, and 12 months of profit |
| **Tasks & bake log** | To-dos, what came out of the oven, waste tracking and a social planner |
| **Settings & backup** | Bakery details for documents, exports, and backup / restore |

**Printable documents** — quotes, invoices, kitchen bake sheets, shopping lists,
allergen labels and a profit & loss statement. They print through the browser,
so "Save as PDF" in the print dialog produces a real PDF with no extra software.

**Exports** — a multi-sheet workbook (`.xls`, opens in Excel, Numbers and
LibreOffice), per-table CSV, and a complete JSON backup you can restore on
another computer.

## Pricing formula

    price = (ingredients + labour + overhead) ÷ (1 − margin %)

Set a margin per product and the app works backwards to the price, so the margin
you ask for is the margin you actually get.

Give a product a recipe — ingredients and quantities per batch — and the
ingredient figure is priced from what those ingredients actually cost in
Inventory. Raise the price of butter and every cake that uses it reprices
itself. Products without a recipe keep a flat ingredients figure instead.

Logging a bake in **Tasks & bake log** deducts that recipe from stock. If
there isn't enough of something, it names the shortfall and asks before
taking stock to zero. A printed bake sheet rolls the day's orders up into a
single "ingredients needed" list, flagging anything you're short of.

## How the data works

Everything is stored in the browser's `localStorage`, on the device that opened
the file. Nothing is transmitted anywhere — there is no network code in the app
at all. That means:

- It works fully offline, including on a phone or tablet.
- Clearing the browser's site data for this file erases the records, so use
  **Settings → Export backup** for anything you care about.
- Moving to a new computer is export on one, import on the other.

The file ships with a sample bakery loaded so every screen is explorable
immediately. **Settings → Clear all data** replaces it with an empty book.

## Running it

Open `index.html` — double-click it, or drag it into a browser window. That's
the whole setup. Tested in Chromium; it uses no framework and no external
requests, so any current browser works.

## Developing

The file is self-contained and ordered: design tokens and stylesheet, then state
and business logic, the render shell, one section per module, the document
engine, exports, and finally the action map that every button dispatches through.

There is no build step. To check a change:

    node --check <(sed -n '/^<script>$/,/^<\/script>$/p' index.html | sed '/^<\/\?script>$/d')

