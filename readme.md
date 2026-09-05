# 🧾 Manager.io — POS (Point of Sale) Extension

> A free, self-contained Point-of-Sale terminal for [Manager.io](https://www.manager.io) — barcode scanning, cart, day register, receipts, and WhatsApp sharing, all in a single HTML file with no backend.

---

## ✨ Features

- **Barcode scanner input** — scan or type a code/name, hit Enter to add to the cart; unrecognized barcodes prompt you to quick-create a new inventory item on the spot
- **Live item search** — type-ahead suggestions by code or name while scanning/searching
- **Quick Add item buttons** — one-tap buttons for fast-moving items
- **Cart management** — adjust quantity and price per line, remove lines, clear cart
- **Customer selection** — search and pick a customer, or fall back to an auto-detected default POS/walk-in customer
- **Payment method selection** — pulls all Cash & Bank accounts, sorts cash accounts first, and defaults to a cash account
- **Auto-generated invoice reference** — timestamp-based reference number (`POS-YYMMDD-HHMMSS`) applied per sale
- **Day Register** — open/close a daily cash drawer session (opening float, closing count, variance) with a printable Day Closing Report
- **Receipts** — on-screen receipt preview, thermal-style print layout, PDF download, and WhatsApp sharing
- **Setup panel** — configure the default customer and required custom fields directly from the extension
- **Light / dark theme toggle**
- **Responsive layout** — usable on tablets and narrower screens (POS-friendly breakpoints)
- **Fully paginated data fetch** — loads all inventory items, customers, and payment accounts across every API page

---

## 🚀 Installation

### Option 1 — Install directly from GitHub Pages

1. Enable GitHub Pages on this repo *(Settings → Pages → Deploy from main branch)*
2. Your extension URL will be:
   ```
   https://ksl1816.github.io/manager-io-pos/
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

1. Click **⬇ Load All Records** — the extension fetches all inventory items, customers, and bank/cash accounts across all pages
2. Use the **⚙ Setup** panel to set a default customer and confirm required custom fields (do this once)
3. Scan a barcode, or type an item code/name in the search box and press **Enter** to add it to the cart
   - If a scanned code isn't recognized, you'll be prompted to quick-create a new inventory item with that code
4. Adjust quantity/price per line as needed, pick or confirm the **customer**, and choose a **payment method**
5. Click **🗄 Register** at the start of the day to open the cash register with an opening float; close it at day's end to reconcile and print the Day Closing Report
6. Complete the sale — a reference number is generated automatically
7. From the receipt screen, **print**, **download as PDF**
8. Toggle 🌙/☀️ to switch between dark and light themes at any time

---

## ⚙️ Technical Details

| Property | Detail |
|---|---|
| File type | Single self-contained `.html` file |
| External libraries | None (no CDN dependencies) |
| APIs used | `/api4/inventory-item-batch`, `/api4/customer-batch`, `/api4/bank-or-cash-account-batch`, `/api4/sales-invoice-batch`, plus starting balances, purchase invoices, debit/credit notes, and write-offs (for stock calculation) |
| Pagination | Full — loops all pages via `next_page_token` |
| Framework | Vanilla HTML / CSS / JavaScript — no build step required |
| Manager.io communication | `postMessage` API (standard extension protocol), with a direct-fetch fallback |
| Local storage | Day Register sessions are stored locally per calendar day; sales made outside this extension are not reflected in the register |

---

## ⚠️ Current Limitations / Status

This extension is under active development and currently runs in **Standalone (cash sales) mode**. Known gaps being worked on:

- Inactive Cash & Bank accounts currently still appear in the payment method list
- Tax rates (e.g. VAT, WHT) are not yet applied to POS sales
- Displayed available stock quantity may not always match Manager's own item balance for complex scenarios
- No dedicated barcode field/setup guidance yet for physical barcode scanners
- Non-inventory items and inventory kits are not yet sellable through the POS
- No configurable default drawer account per payment method (currently defaults to the first cash account found)
- No dedicated mobile view (desktop/tablet layout only for now)

See the community forum thread for ongoing discussion and updates.

---

## ⚠️ Disclaimer

This extension is an independent, community-built tool and is **not officially affiliated with or endorsed by Manager.io**. It is provided free of charge, as-is. Always verify sales and financial figures against your official Manager.io reports before making business decisions.

---

## 🛠️ Built With

This extension was built using the **[Manager.io Developer Toolkit](https://github.com/ksl1816/manager-developer-toolkit-extenstion)** — a companion extension that lets you explore Manager.io API endpoints and generate AI prompts for building extensions like this one.

---

## 📄 License

Free to use, modify, and share. Attribution appreciated but not required.

---

## 👤 Author

**ksl1816** — [github.com/ksl1816](https://github.com/ksl1816)

*Contributions, bug reports, and feature suggestions are welcome — open an issue or pull request.*
