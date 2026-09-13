# POS Terminal for Manager.io — User Guide (v2.2)

This guide covers everyday use of the extension, in the order you'd actually touch things during a shift. It describes only what the extension actually does — nothing here is aspirational or simplified for effect.

---

## 1. First-time setup

Do this once per business, not every session.

1. Open the extension inside Manager.io.
2. Click **⬇ Load Items & Customers** (top toolbar). This fetches every inventory item, non-inventory item, customer, and bank/cash account, and calculates current stock quantities from your transaction history. On a large business this can take a little while — the progress bar and status text show what stage it's at.
3. Open **⚙ Setup** (top toolbar). If no customer with code `999` or named "POS" exists yet, click **Create Default "POS" Customer**. Every walk-in sale (no specific customer picked) posts against this customer, with the actual person's name/phone (if you type them in) saved in the invoice's description field — there are no custom fields to configure for this; it's automatic.
4. Toggle 🌙/☀️ in the top bar if you want light mode instead of dark.

You don't need to repeat step 2/3 again unless you're setting this up on a different business.

---

## 2. Making a sale

**Adding items** — three ways:
- **Scan a barcode.** The scanner input is always focused and ready; a scan (or manually typed code) followed by Enter adds the item.
- **Type to search.** Start typing a code or item name in the same box — a dropdown of matches appears; click one or arrow-key + Enter to select.
- **Quick Add panel** (left side of the Current Sale panel). Tap the **Inventory** or **Non-Inventory** tab, then tap any tile to add that item instantly. This list shows every item that has a default sale price set in Manager, sorted highest price first.

If you scan/type a code that doesn't match anything, you'll be prompted to create it on the spot — pick Inventory (tracked stock) or Non-Inventory (services/fees, no stock tracking), fill in code/name/price, and it's added to both Manager and the current cart in one step.

**Editing the cart** (right side): for each line you can change:
- **Qty** — the quantity being sold
- **Price** — the unit price for this specific sale (doesn't change the item's default price in Manager, just this transaction)
- **Total** — type the total amount you want to charge for the line instead, and Qty recalculates automatically from the existing price (rounded to 2 decimal places). Useful when you know the total you're charging but not the exact quantity.

Remove a line with the **×** button, or clear the whole cart with **Clear**.

**Customer, payment method, walk-in details** (right panel):
- Type in the customer search box to pick a specific customer, or leave it on the auto-detected default POS/walk-in customer.
- Pick a payment method from the dropdown (cash accounts are listed first).
- If it's a walk-in sale, you can type a name/phone — these get saved as plain text in the invoice description, not as separate Manager fields.

Click **Complete Sale** to finish. A reference number (`POS-YYMMDD-HHMMSS`) is generated automatically, and the receipt screen opens.

---

## 3. The receipt screen

After a sale, you'll see:
- **🖨 Print** — opens a print-formatted 80mm thermal layout in a new window and triggers the browser's print dialog.
- **📄 Save as PDF** — downloads an actual PDF file (not an HTML file requiring a manual "print to PDF" step) generated in your browser.
- **💳 Apply Credit** — only relevant if this customer has a credit balance sitting on a *different* invoice (from a prior over-return or overpayment). Lets you apply some or all of it toward the invoice you just created; the receipt updates to show Total, Credit Applied, and remaining Balance Due.
- **💰 Record Payment** — opens a payment form, not a single click-and-done button. See below.

### Recording payment (multi-line, with Change)

Clicking Record Payment opens a small table:

| Account | Amount | Tendered | Change |
|---|---|---|---|

- The first row defaults to whichever payment method you picked at checkout, pre-filled with the full amount due.
- Type what the customer actually handed you into **Tendered** — **Amount** (what's actually applied to the invoice) and **Change** (what to hand back) calculate automatically. Amount is capped so it can never exceed what's actually due, even if Tendered is larger.
- Need to split the payment across more than one method (part cash, part card)? Click **+ Add Line** — the new line's Amount auto-fills with whatever's still left to allocate.
- A running summary at the bottom shows **Entered / Remaining (or Fully Paid) / Change Due** across all lines combined.
- Click **Post Payment(s)** — each line posts as its own real Receipt in Manager.

If the invoice was fully covered by Applied Credit already, Record Payment is disabled and shows "✓ Covered by Credit" instead.

---

## 4. Day Register

Click **🗄 Register**. This is a live, read-only-by-design view of a single day's activity — there is nothing to open, close, or manually enter here; every figure is computed from real Manager transactions the moment you open or refresh it.

For the selected date, you'll see:
- **Sales Invoiced**, **Cash/Receipts Collected**, **Returns/Credit Notes**, and (if any happened that day) **Cash Refunds Paid Out**
- **Net Cash Movement** — Collected minus Refunded for the day; this is the actual cash-in-the-drawer change, not a manually-reconciled "expected vs actual" figure
- A card per Sales Invoice, each showing:
  - Reference, customer, and a **Paid / Due X / Credit X** status badge
  - **Items** — expand to see the line items sold
  - **Receipts (N)** — only appears if the invoice was paid in more than one transaction; expands to show each individual receipt with its account, date, and amount
  - **Receipt** button (if still due) to record a payment against it directly, right there
  - **💳 Adjust** button (if it has a credit balance) to refund or reassign it
  - **↩ Return** to post a return against it
- A card per Return (Credit Note) for that date, showing which invoice and customer it was against

Change the date with the picker at the top; click **↻ Refresh** to re-pull the latest data; click **🖨 Print Day Summary** for an 80mm thermal-formatted printable version of everything above.

---

## 5. Sales History

Click **🕘 History**. Unlike the Day Register (one date at a time), this searches across *all* invoices ever created.

- Set a **From**/**To** date range, and/or type into **Search Ref / Customer**.
- Press **Enter** in the search box, or click **Filter**, to apply.
- The summary shows Invoices in range, Total Invoiced, Total Returned, and Total Received for the filtered set.
- Each invoice appears as the same kind of card as the Day Register, with the same Items/Receipts drill-downs and action buttons.
- If more than 500 invoices match, only the first 500 show — narrow your date range to see the rest.
- **↻ Refresh** re-fetches from Manager (useful if changes were made outside this session).

---

## 6. Sales Analysis

Click **📈 Analysis**. Pick a date range and get, computed entirely in your browser (no external charting service):
- Invoice count, total quantity sold, average sale value, and total revenue
- Top 10 items by revenue, and top 10 by quantity, each with an inline bar comparison
- A day-by-day revenue breakdown table

---

## 7. Returns and credit balances

**To return goods from a past sale:**
1. Either find the invoice via **↩ Returns** (a dedicated search by reference or customer name, useful when you don't want to hunt through Register/History first), or use the **↩ Return** button directly on any invoice card in Register/History.
2. Enter the quantity being returned for each line — you can't return more than what's left un-returned on that line (the extension checks every existing Credit Note against that invoice first).
3. Confirm the return date and reference, then post. This creates a real Credit Note in Manager.

**When Receipts + Returns exceed what was owed**, the invoice shows a **Credit** balance (in blue) instead of a misleading "Due: 0.00". Click **💳 Adjust** on that invoice to resolve it two ways:
- **💵 Refund to Customer** — pay the credit out of a chosen bank/cash account (posts a real Manager Payment).
- **🔀 Apply to Another Invoice** — search for a different invoice the same customer owes on, and move some or all of the credit there (posts a balanced Journal Entry, no money physically moves).

---

## 8. Editing an existing invoice

Click the ✎ icon next to any invoice reference (Day Register or History).

You can change:
- The **Reference** and **Issue Date**
- Any existing line's **Qty** or **Unit Price**, or remove a line entirely
- **Add a brand-new line** — type an item name/code into the search box above the table and press Enter, then click **+ Add** next to the item you want from the results list

Click **Save Changes** to write it back to Manager. Note: editing an already-paid invoice's total amount does **not** automatically adjust any Receipts already recorded against it — check those separately if you change a paid invoice's total.

*Purchase Invoice editing is not available* — it was removed because it was never reliably wired up and its write behavior was never independently confirmed against Manager's API. Edit Purchase Invoices directly in Manager.

---

## 9. Things worth knowing before you rely on this in production

- **Accounts Receivable resolution.** Every Receipt/Payment/Journal Entry needs the correct AR control account. The extension tries the customer's own configured account first, then falls back through a few automatic lookups, and only reaches a hardcoded fallback account if all of those come back empty. If your business has multiple AR accounts across customer groups, double-check the very first few transactions land in the right one.
- **Return-quantity tracking** assumes Credit Notes created outside this extension also set the invoice reference field — if you post returns manually in Manager without linking them to the invoice, "already returned" quantities here may undercount for those.
- **Credit/Due calculations** net four sources — Receipts, Credit Notes, Journal Entries, and refund Payments — all read live from Manager. Money that moved some other way (a manual entry that doesn't reference the invoice) won't show up in these figures.
- **Stock quantities** shown are calculated from your transaction history (starting balances, purchases, sales, returns, write-offs) and may not always match Manager's own item balance in complex scenarios — treat it as a strong indicator, not a guaranteed audit-grade figure.
- **PDF receipts with Urdu/Arabic names**: the correct script now renders (instead of garbled symbols), but each letter appears in isolated form rather than fully joined cursive Nastaliq — legible, not calligraphically perfect.

Always verify sales, receipts, returns, and credit adjustments against your official Manager.io reports before relying on them for accounting or reconciliation. This is a free, independent, community-built tool — not affiliated with or endorsed by Manager.io.
