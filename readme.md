# 🧾 POS extension for Manager

> An opensource, independent, community-built Point-of-Sale extension for [Manager](https://www.manager.io) (Manager.io) — barcode scanning, cart, a Manager-backed Day Register, receipts, sales history, sales analysis, sale returns, and credit-balance handling, in a single HTML file with no backend. It is **not** an official Manager product and is not affiliated with or endorsed by Manager.io.
>
> The jsPDF library is bundled inline, so PDF generation works fully offline. The only thing loaded from the network is web fonts (Google Fonts), which degrade gracefully to system fonts when offline — so the extension is *nearly* self-contained rather than 100% air-gapped.

**Current version: v2.4**

This README is written to match exactly what the current `index.html` does — every API endpoint it calls, every assumption it makes, and every known limitation — so there's a clear, honest record of the extension's behavior before you put it in front of real transactions.

---

## ✨ Features

- **Barcode scanner input** — scan or type a code/name, hit Enter to add to the cart; unrecognized barcodes prompt you to quick-create a new item on the spot
- **Live item search** — type-ahead suggestions by code or name while scanning/searching
- **Sells inventory *and* non-inventory items** — non-inventory items (services, fees, etc.) are loaded, searchable, and sellable alongside stocked items; the Quick Add modal has an Inventory / Non-Inventory toggle so you can create either on the fly
- **Quick Add — split-panel layout** — the Current Sale panel is divided into a left Quick Add catalog (tabbed Inventory / Non-Inventory, tap-to-add, full vertical scroll, not capped at a handful of items) and the actual cart on the right, so both get real usable space instead of one squeezing the other
- **Cart management** — adjust **quantity**, **unit price**, or the **line Total** directly (editing Total works backwards to recalculate Qty from the existing price, rounded to 2 decimal places), remove lines, clear cart
- **Customer selection** — search and pick a customer, or fall back to an auto-detected default POS/walk-in customer (code `999` or named "POS")
- **Payment method selection** — pulls all Cash & Bank accounts, sorts cash accounts first, and defaults to a cash account
- **Auto-generated invoice reference** — timestamp-based reference number (`POS-YYMMDD-HHMMSS`) applied per sale
- **Day Register (live cash-movement report)** — reports the day's cash movement, not a till reconciliation: there is no opening float, closing count, or counted-cash variance (a manual number with no link to the real ledger wasn't trustworthy data). The register view shows every Sales Invoice for the selected date pulled live from Manager as a card per invoice — Total / Received / Due, an item drill-down, an expandable **Receipts** breakdown (Receipt 1, Receipt 2… when a sale was paid in more than one transaction), an ✎ edit button, a "Receipt" action, a "↩ Return" action, and a "💳 Adjust" action when a credit balance exists — alongside a Returns (Credit Notes) list, itemized **Receipts Posted This Day** and **Payments / Refunds Posted This Day** lists, and a **Net Cash Movement** figure
- **Cash-drawer-accurate Net Cash Movement** — the day's cash figure is selected by each **Receipt's own date** (not the invoice date) and counts **only Cash-account receipts**; Bank-account receipts are excluded and shown separately as "Bank Receipts". Net Cash Movement = cash collected − cash refunds paid out that day. A later instalment against an older invoice therefore lands on the day the Receipt was posted, and the itemized Receipts/Payments lists make every figure traceable. (Invoice **balances** still net *all* receipts linked to the invoice regardless of date — only the Day Register cash calculation is date/account-scoped.) The on-screen register and the printed Day Summary share one calculation function, so they can never diverge.
- **Multi-line payments with a Change calculator** — both at checkout (right on the receipt screen after a sale) and against any invoice from the Day Register, record several payments in one action (e.g. part cash, part card). Tendered drives the math: type what the customer physically handed over, and the applied Amount + Change are worked out automatically (capped so Amount can never exceed what's actually due), with a running Entered / Remaining / Change Due summary across all lines. When cash is tendered at checkout, **TENDERED PAYMENT** and **CHANGE** print on the receipt/bill, and both figures are saved into the Manager Receipt's **Description** field (Manager has no native change-given field), so the cash-handling record persists on the document
- **Editable Receipt Date** — both payment flows (checkout Record Payment and the Day Register receipt form) let you set the date the receipt is posted under, so a payment (including a later instalment against an older invoice) can be dated to the day it was actually taken
- **Sales History** — browse and filter *all* past Sales Invoices (not just the current day) by date range or by reference/customer text (press Enter or click Filter), with the same card layout, item drill-down, Receipts breakdown, edit, return, and adjust actions
- **Sales Analysis** — pick a date range and get total revenue, quantity sold, invoice count, average sale value, top 10 items by revenue and by quantity, and a revenue-by-day breakdown — all computed client-side, no chart library required
- **Returns Search** — a standalone search (by invoice reference or customer name, across the entire invoice history) to jump straight into recording a return or adjusting a credit balance, without needing to locate the invoice in the Register or History lists first
- **Sale Returns** — post a Credit Note against any past Sales Invoice, per line item, with automatic tracking of quantity already returned so you can't over-return a line
- **Credit balance handling** — when Receipts + Returns exceed an invoice's total, the invoice shows a "Credit" balance instead of a false "0.00 due", with two ways to resolve it:
  - **Refund to Customer** — posts a Manager Payment paying the credit out of a chosen bank/cash account
  - **Apply to Another Invoice** — posts a balanced Journal Entry moving the credit from the source invoice to a different invoice the customer owes on
- **Apply Credit to a new sale** — from the receipt screen right after a sale, apply a customer's existing credit balance (from a prior over-return/overpayment) toward the invoice just created; the receipt updates to show Total, Credit Applied, and Balance Due, and Record Payment only ever collects what's left after the credit
- **Real invoice editing, including adding new lines** — edit a Sales Invoice's reference, date, existing line qty/price, *and* add brand-new line items via a search box right in the edit modal — saved back to Manager via `PUT /api4/sales-invoice`
- **Printable Day Register summary** — an 80mm thermal-formatted summary of the day (sales, cash collected, bank receipts, cash refunds, returns, net cash movement, the itemized Receipts and Payments lists, and each invoice's customer and Due/Credit status), computed with the exact same function as the on-screen register, with the business name and address pulled live from Manager's Business Details
- **Receipts** — on-screen receipt preview, thermal-style print layout showing the actual business name/address, and a genuine PDF download generated client-side via jsPDF. Note: non-Latin scripts (Urdu/Arabic) may not render in the PDF — see Known Limitations
- **Setup panel** — configure the default POS/walk-in customer directly from the extension
- **Built-in User Guide** — a **User Guide** button in the header opens a full how-to reference inside the extension (no internet or separate file needed)
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
2. Use the **⚙ Setup** panel to confirm/create a default POS/walk-in customer (do this once)
3. Scan a barcode, type an item code/name and press **Enter**, or tap a tile in the **Quick Add** panel on the left (Inventory / Non-Inventory tabs) to add it to the cart on the right
   - If a scanned code isn't recognized, you'll be prompted to quick-create a new item (choose Inventory or Non-Inventory) with that code
4. Adjust **Qty**, **Price**, or the line **Total** directly in the cart (editing Total recalculates Qty from the existing price), pick or confirm the **customer**, and choose a **payment method**
5. Complete the sale — a reference number is generated automatically
6. From the receipt screen: **print**, **save as PDF** (a real PDF file, generated in-browser), **💳 Apply Credit** (if the customer has an existing credit balance elsewhere), or **💰 Record Payment** — which opens a multi-line payment form where Tendered drives Amount and Change automatically, with an editable **Receipt Date**
7. Click **🗄 Register** to open the Day Register for any date — review that date's Sales Invoices (with an expandable Receipts breakdown per invoice), the itemized **Receipts** and **Payments / Refunds** posted that day, and Returns; record receipts (with an editable Receipt Date); and **🖨 Print Day Summary** when done
8. Click **🕘 History** to search and browse past invoices beyond the current day (press Enter or click Filter to search), **📈 Analysis** for revenue/quantity breakdowns and top-selling items, and **↩ Returns** to search any invoice directly for a return or credit adjustment
9. Use **↩ Return** to post a Credit Note against a past invoice when a customer returns goods, and **💳 Adjust** wherever a Credit balance appears to refund or reassign it
10. Click **✎** on any invoice to edit its reference, date, existing lines, or add a brand-new line via the search box in the edit modal
11. Toggle 🌙/☀️ to switch between dark and light themes at any time

---

## ⚙️ Technical Details

| Property | Detail |
|---|---|
| File type | Single `index.html` file (jsPDF bundled inline) |
| External libraries | jsPDF **bundled inline** (no CDN) so PDF export works offline. The only network load is Google Fonts (a stylesheet; degrades to system fonts offline). No analytics, tracking, or other third-party scripts, and no external executable JavaScript is loaded at runtime. |
| Framework | Vanilla HTML / CSS / JavaScript — no build step required |
| Manager.io communication | `postMessage` API (standard extension protocol), with a direct-fetch fallback when running inside Manager's iframe context (`managerAppContext.apiEndpoint`) |
| Pagination | Full — loops all `-batch` endpoints via `next_page_token` / `Skip` |
| Day Register storage | **Not local, and nothing is manually entered.** No Opening/Closing Cash figure is typed in or stored anywhere — the register is computed live every time it's opened: Sales Invoices, Receipts, Returns, and refund Payments for the selected date are fetched fresh from Manager, never cached in the browser between sessions |
| Day Register cash calc | Net Cash Movement is derived from a single shared function used by both the screen and the printout. It selects Receipts and Payments by the **transaction's own date**, and counts only **Cash accounts** toward cash movement (Bank receipts are shown separately, excluded from the drawer figure). Invoice balances are unaffected — they still net all receipts linked to the invoice regardless of date |
| Cash vs bank detection | The physical cash-drawer account(s) are chosen **explicitly by the user** in ⚙ Setup and saved in the browser (localStorage). The Day Register counts only receipts/payments into those accounts as cash. Until the user configures this, it falls back to a best-effort guess from account features and the register shows a notice that cash/bank is being guessed. |

### API endpoints used (reads)

`/api4/inventory-item-batch`, `/api4/non-inventory-item-batch`, `/api4/customer-batch`, `/api4/bank-or-cash-account-batch`, `/api4/sales-invoice-batch`, `/api4/purchase-invoice-batch`, `/api4/receipt-batch`, `/api4/credit-note-batch`, `/api4/journal-entry-batch`, `/api4/payment-batch`, `/api4/business-details`, `/api4/balance-sheet-account-batch`, `/api4/control-account-batch`, `/api4/text-custom-field-batch`, plus starting balances, debit notes, and inventory write-offs (used only for computing on-hand stock quantities).

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

---

## ⚠️ Important Assumptions & Things to Verify Before Going Live

These are the specific places where the extension makes a judgment call or a fallback assumption, rather than something guaranteed to be correct for every Manager.io business. Please review each one against your own Chart of Accounts before relying on this in production:

- **Accounts Receivable account resolution.** Every Receipt, Payment, and Journal Entry that touches a customer's balance needs the correct AR control account. The extension resolves this, in order: (1) the customer's own configured control account, (2) an AR account already used on an existing Receipt for that customer, (3) an AR account used on *any* existing Receipt, (4) the Chart of Accounts' flagged `isAccountsReceivable` account via `balance-sheet-account-batch` / `control-account-batch`. **There is no hardcoded fallback account.** If all four lookups come back empty, the transaction is **stopped and an error is shown** rather than posting to a fixed UUID that might be wrong for your business — a clear failure you can act on beats silently bad ledger data. (A transient network error during the lookup is not cached, so it can be retried.) To resolve it, post one transaction manually in Manager.io first: open a Sales Invoice and use its **Copy to → New Receipt** button, which records the correct Accounts Receivable account — the extension then reuses that account for subsequent transactions.
- **Cash vs bank is an explicit choice.** The Day Register's cash figures only count your physical cash drawer, so you designate which account(s) are the drawer in ⚙ Setup → **Cash Drawer Accounts** (saved in the browser). This avoids any guessing: Manager's account form has no cash/bank field, and the API's `isBankAccount` flag is not dependable (it returns true even for a plain "Cash in Hand" account). If you have not configured this yet, the register falls back to a heuristic (an account with "Can have pending transactions" or an IBAN is treated as bank; otherwise cash) and displays a notice that it is guessing — set your drawer account(s) to make it exact.
- **The Day Register is a cash-movement report, not a till reconciliation.** It reports cash received and paid out for the selected date, computed live from Manager. It deliberately has no opening float, no closing count, and no counted-cash variance — there is nothing to open or close. To reconcile the physical drawer, count the cash and compare it against the day's Net Cash Movement yourself.
- **Credit-note return quantities** are tracked by comparing an invoice's original line quantities against the sum of quantities on *all* existing Credit Notes referencing that invoice — this assumes Credit Notes made outside this extension also set the `salesInvoice` field on the note; if they don't, "already returned" may under-count for those specific notes.
- **Credit/Due calculations** (shown in Register, History, Returns Search, and Apply Credit) net four sources: Receipts, Credit Notes, Journal Entries, and Payments (refunds) — anything carrying `accountsReceivableSalesInvoice`, all read via their respective `-batch` endpoints. If money moved some other way in Manager (e.g. a manual entry without that field set), it won't be reflected in these figures.

---

## ⚠️ Known Limitations

- Tax rates (e.g. VAT, WHT) are not yet applied to POS sales
- Displayed available stock quantity may not always match Manager's own item balance for complex scenarios
- No dedicated barcode field/setup guidance yet for physical barcode scanners (the barcode input works but some scanners may need configuration)
- Inventory kits are not yet sellable through the POS (non-inventory items now are)
- No configurable default drawer account per payment method (currently defaults to the first cash account found)
- No dedicated mobile view (desktop/tablet layout only for now, though the Day Register/History/Analysis/Returns modals are responsive)
- **PDF receipts do not reliably render Urdu/Arabic (or other non-Latin) names.** jsPDF has no Arabic text-shaping engine, and in practice such names may come out blank/empty or as isolated, unjoined characters in the generated PDF. Use the printed/thermal receipt for those names, and verify before relying on a PDF receipt that contains non-Latin text.
- Quick Add shows up to 500 items per tab (Inventory / Non-Inventory) sorted by price — a safety cap for very large catalogs, not a hard product limit for typical businesses

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
