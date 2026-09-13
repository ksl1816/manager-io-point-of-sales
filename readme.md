# 🧾 Manager.io — POS (Point of Sale) Extension

> A free, self-contained Point-of-Sale terminal for [Manager.io](https://www.manager.io) — barcode scanning, cart, a fully Manager-backed Day Register, receipts, sales history, sales analysis, sale returns, and credit-balance handling, all in a single HTML file with no backend.

**Current version: v2.1 · 2026-09-12**

This README is written to match exactly what the current `index.html` does — every API endpoint it calls, every assumption it makes, and every known limitation — so there's a clear, honest record of the extension's behavior before you put it in front of real transactions.

---

## ✨ Features

- **Barcode scanner input** — scan or type a code/name, hit Enter to add to the cart; unrecognized barcodes prompt you to quick-create a new item on the spot
- **Live item search** — type-ahead suggestions by code or name while scanning/searching
- **Sells inventory *and* non-inventory items** — non-inventory items (services, fees, etc.) are loaded, searchable, and sellable alongside stocked items; the Quick Add modal has an Inventory / Non-Inventory toggle so you can create either on the fly
- **Quick Add item buttons** — one-tap buttons for fast-moving items
- **Cart management** — adjust quantity and price per line, remove lines, clear cart
- **Customer selection** — search and pick a customer, or fall back to an auto-detected default POS/walk-in customer (code `999` or named "POS")
- **Payment method selection** — pulls all Cash & Bank accounts, sorts cash accounts first, and defaults to a cash account
- **Auto-generated invoice reference** — timestamp-based reference number (`POS-YYMMDD-HHMMSS`) applied per sale
- **Day Register (Manager-backed, not local)** — opening/closing cash figures are written to the Business Details record itself (`customFields2.decimals`), namespaced per calendar date, so the register survives across devices and reinstalls. Yesterday's closing is automatically suggested as today's opening float. The register view shows every Sales Invoice for the selected date pulled live from Manager — Total / Received / Due, an item drill-down, an ✎ edit button, a "Receipt" action, a "↩ Return" action, and a "💳 Adjust" action when a credit balance exists — alongside a Returns (Credit Notes) column for that date
- **Multi-line receipts** — record several receipts against a single Sales Invoice in one action (e.g. part cash, part bank), each posted as its own Manager Receipt
- **Sales History** — browse and filter *all* past Sales Invoices (not just the current day) by date range or by reference/customer text, with the same item drill-down, edit, return, and adjust actions
- **Sales Analysis** — pick a date range and get total revenue, quantity sold, invoice count, average sale value, top 10 items by revenue and by quantity, and a revenue-by-day breakdown — all computed client-side, no chart library required
- **Returns Search** — a standalone search (by invoice reference or customer name, across the entire invoice history) to jump straight into recording a return or adjusting a credit balance, without needing to locate the invoice in the Register or History lists first
- **Sale Returns** — post a Credit Note against any past Sales Invoice, per line item, with automatic tracking of quantity already returned so you can't over-return a line
- **Credit balance handling** — when Receipts + Returns exceed an invoice's total, the invoice shows a "Credit" balance instead of a false "0.00 due", with two ways to resolve it:
  - **Refund to Customer** — posts a Manager Payment paying the credit out of a chosen bank/cash account
  - **Apply to Another Invoice** — posts a balanced Journal Entry moving the credit from the source invoice to a different invoice the customer owes on
- **Apply Credit to a new sale** — from the receipt screen right after a sale, apply a customer's existing credit balance (from a prior over-return/overpayment) toward the invoice just created; the receipt updates to show Total, Credit Applied, and Balance Due, and Record Payment only ever collects what's left after the credit
- **Real invoice editing** — edit a Sales Invoice's reference, date, and line qty/price directly from the extension, saved back to Manager via `PUT /api4/sales-invoice`
- **Printable Day Register summary** — an 80mm thermal-formatted summary of the day (opening/closing cash, sales, receipts, returns), with the business name and address pulled live from Manager's Business Details
- **Receipts** — on-screen receipt preview, thermal-style print layout showing the actual business name/address, and a genuine PDF download generated client-side via jsPDF
- **Setup panel** — configure the default customer and required custom fields directly from the extension
- **Light / dark theme toggle**
- **Responsive layout** — usable on tablets and narrower screens (POS-friendly breakpoints)
- **Fully paginated data fetch** — loads all inventory items, non-inventory items, customers, and payment accounts across every API page

---

## 🚀 Installation

### Option 1 — Install directly from GitHub Pages

1. Enable GitHub Pages on this repo *(Settings → Pages → Deploy from main branch)*
2. Your extension URL will be:
   ```
   https://ksl1816.github.io/manager-io-point-of-sales/
   ```
3. In Manager.io go to **Settings → Extensions → Add Custom Extension**
4. Paste the URL above and save

### Option 2 — Download and host yourself

1. Download `index.html` from this repository
2. Host it on any static hosting service:
   - **GitHub Pages** — upload to any public repo and enable Pages
   - **Netlify** — drag and drop the file at [netlify.com](https://netlify.com)
   - **Vercel** — drag and drop at [vercel.com](https://vercel.com)
3. Copy the public URL and add it as a custom extension in Manager.io

---

## 🧭 How to Use

Once installed inside Manager.io:

1. Click **⬇ Load Items & Customers** — the extension fetches all inventory items, non-inventory items, customers, and bank/cash accounts across all pages
2. Use the **⚙ Setup** panel to set a default customer and confirm required custom fields (do this once)
3. Scan a barcode, or type an item code/name in the search box and press **Enter** to add it to the cart
   - If a scanned code isn't recognized, you'll be prompted to quick-create a new item (choose Inventory or Non-Inventory) with that code
4. Adjust quantity/price per line as needed, pick or confirm the **customer**, and choose a **payment method**
5. Complete the sale — a reference number is generated automatically
6. From the receipt screen: **print**, **save as PDF** (a real PDF file, generated in-browser), **💳 Apply Credit** (if the customer has an existing credit balance elsewhere), or **💰 Record Payment**
7. Click **🗄 Register** to open the Day Register for any date — set the opening cash (or accept the suggested carry-forward from the previous day's closing), review that date's Sales Invoices and Returns, record receipts, set the closing cash, and **🖨 Print Day Summary** when done
8. Click **🕘 History** to search and browse past invoices beyond the current day, **📈 Analysis** to see revenue/quantity breakdowns and top-selling items, and **↩ Returns** to search any invoice directly for a return or credit adjustment
9. Use **↩ Return** to post a Credit Note against a past invoice when a customer returns goods, and **💳 Adjust** wherever a Credit balance appears to refund or reassign it
10. Toggle 🌙/☀️ to switch between dark and light themes at any time

---

## ⚙️ Technical Details

| Property | Detail |
|---|---|
| File type | Single self-contained `index.html` file |
| External libraries | jsPDF (from cdnjs, used only to generate the receipt PDF) and Google Fonts. No analytics, tracking, or other third-party scripts. |
| Framework | Vanilla HTML / CSS / JavaScript — no build step required |
| Manager.io communication | `postMessage` API (standard extension protocol), with a direct-fetch fallback when running inside Manager's iframe context (`managerAppContext.apiEndpoint`) |
| Pagination | Full — loops all `-batch` endpoints via `next_page_token` / `Skip` |
| Day Register storage | **Not local.** Opening/closing cash figures are stored on Manager's own Business Details record (`customFields2.decimals`), namespaced per date. Nothing about the register, history, or analysis is cached in the browser between sessions — invoice/receipt/return/journal lists are fetched fresh from Manager every time a view is opened or refreshed |

### API endpoints used (reads)

`/api4/inventory-item-batch`, `/api4/non-inventory-item-batch`, `/api4/customer-batch`, `/api4/bank-or-cash-account-batch`, `/api4/sales-invoice-batch`, `/api4/purchase-invoice-batch`, `/api4/receipt-batch`, `/api4/credit-note-batch`, `/api4/journal-entry-batch`, `/api4/business-details`, `/api4/balance-sheet-account-batch`, `/api4/control-account-batch`, `/api4/text-custom-field-batch`, plus starting balances, debit notes, and inventory write-offs (used only for computing on-hand stock quantities).

### API endpoints used (writes)

| Endpoint | Used for |
|---|---|
| `POST /api4/sales-invoice` | Completing a sale |
| `PUT /api4/sales-invoice` | Editing an existing Sales Invoice's reference/date/lines |
| `POST /api4/receipt` | Recording a payment against a Sales Invoice (checkout "Record Payment" and the Day Register's multi-line receipt form) |
| `POST /api4/credit-note` | Posting a Sale Return |
| `POST /api4/payment` | Refunding a customer's credit balance out of a bank/cash account |
| `POST /api4/journal-entry` | Moving a credit balance from one invoice to another (Adjust / Apply Credit) |
| `POST /api4/inventory-item` | Quick-creating a new inventory item |
| `POST /api4/non-inventory-item-batch` | Quick-creating a new non-inventory item |
| `POST /api4/customer` | Creating the default "POS" walk-in customer from Setup |
| `PUT /api4/business-details` | Saving Day Register opening/closing cash amounts |

---

## ⚠️ Important Assumptions & Things to Verify Before Going Live

These are the specific places where the extension makes a judgment call or a fallback assumption, rather than something guaranteed to be correct for every Manager.io business. Please review each one against your own Chart of Accounts before relying on this in production:

- **Accounts Receivable account resolution.** Every Receipt, Payment, and Journal Entry that touches a customer's balance needs the correct AR control account. The extension resolves this, in order: (1) the customer's own configured control account, (2) an AR account already used on an existing Receipt for that customer, (3) an AR account used on *any* existing Receipt, (4) the Chart of Accounts' flagged `isAccountsReceivable` account via `balance-sheet-account-batch` / `control-account-batch`. If all of those fail, it falls back to a fixed account reference in `getDefaultARAccount()` that has held up across every business tried so far. In practice this fallback path is only reached if all of the earlier automatic lookups come back empty.
- **Credit-note return quantities** are tracked by comparing an invoice's original line quantities against the sum of quantities on *all* existing Credit Notes referencing that invoice — this assumes Credit Notes made outside this extension also set the `salesInvoice` field on the note; if they don't, "already returned" may under-count for those specific notes.
- **Credit/Due calculations** (shown in Register, History, Returns Search, and Apply Credit) net three sources: Receipts, Credit Notes, and Journal Entries carrying `accountsReceivableSalesInvoice`. If a payment against an invoice was recorded some other way in Manager (e.g. a manual Journal Entry without that field set, or a different transaction type entirely), it won't be reflected in these figures.

---

## ⚠️ Known Limitations

- Tax rates (e.g. VAT, WHT) are not yet applied to POS sales
- Displayed available stock quantity may not always match Manager's own item balance for complex scenarios
- No dedicated barcode field/setup guidance yet for physical barcode scanners (the barcode input works but some scanners may need configuration)
- Inventory kits are not yet sellable through the POS (non-inventory items now are, as of v2.1)
- No configurable default drawer account per payment method (currently defaults to the first cash account found)
- No dedicated mobile view (desktop/tablet layout only for now, though the Day Register/History/Analysis/Returns modals are responsive)

---

## ⚠️ Disclaimer

This extension is an independent, community-built tool and is **not officially affiliated with or endorsed by Manager.io**. It is provided free of charge, as-is. Always verify sales, receipts, returns, credit adjustments, and register totals against your official Manager.io reports before relying on them for accounting or reconciliation.

The POS creates and edits real Sales Invoices, Receipts, Payments, Credit Notes, Journal Entries, and Business Details fields via the postMessage bridge — exercise care when testing and use a safe business instance for development before pointing it at live data.

---

## 🛠️ Built With

This extension was built using the **[Manager.io Developer Toolkit](https://github.com/ksl1816/manager-developer-toolkit-extenstion)** — a companion extension that lets you explore Manager.io API endpoints and generate AI prompts for building extensions like this one.

---

## 📄 License

Free to use, modify, and share. Attribution appreciated but not required.

---

## 👤 Author

**ksl1816** — [github.com/ksl1816](https://github.com/ksl1816)

Contributions, bug reports, and feature suggestions are welcome — open an issue or pull request.
