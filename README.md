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
| **Payments & performance** | Booked sales, cash received, outstanding and expenses, with 12 months of profit |
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

## How the money is counted

Four figures, kept deliberately separate, because they answer different
questions:

| Figure | What it means |
| --- | --- |
| **Booked sales** | Orders the customer has agreed to — booked or paid. Dated by when the order is wanted. |
| **Cash received** | Money actually taken, recorded payment by payment and dated by when it arrived. |
| **Outstanding** | Booked sales not yet paid for. An enquiry or an open quote is never a debt. |
| **Net profit** | Booked sales − cost of goods − expenses. |

Sales, cost of goods and profit count in the month the order is **wanted** —
its fulfilment date. Cash received counts in the month the payment was
**taken**. That is why the two can differ, and it is deliberate: it tells you
what the bakery earned in a month separately from what landed in the till.
It is a business performance view to run the bakery by, not a formal set of
accounts; a bookkeeper or tax return may count the same money on different
dates.

Booking an order does not record a payment. `depositRequired` is what the
customer was *asked* for; cash exists only as payment records against the
order, so the app can never show money that nobody handed over. Partial
payments are ordinary records: pay half, and the order stays booked with the
balance outstanding until the rest arrives.

When an order is booked or paid, its cost of goods is **frozen** from the
recipe at that moment. Ingredient prices can rise afterwards without
rewriting last quarter's profit. New quotes still price from today's costs.

Moving an order to **Paid** records whatever is still owed as cash taken
today, so it always asks first and names the exact amount — whether you use
the button, the status list, the stage arrow or drag a card across the board.

Quotes expire `quoteValidDays` after the day they were raised — not on the
date the cake is wanted, which may be months later.

## Protecting history

Nothing that carries financial history is destroyed silently:

- A customer or product with orders behind it is **archived**, not deleted —
  hidden from new orders, still readable in the history. Records with no
  history are deleted outright.
- Deleting an ingredient that a recipe uses is blocked: the app lists the
  affected products and makes removing it from those recipes the explicit
  choice.
- Deleting a bake returns exactly the stock it took, from a deduction record
  stored on the bake itself.
- Deleting an order warns when it would also delete recorded payments.
- Restoring a backup, clearing all data and reloading the sample can each be
  undone from **Settings → Backup & restore** for the rest of the session.

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

