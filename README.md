# 📊 RFQ Analysis — Power BI Dashboard

An end-to-end procurement analytics solution built for **Procmart Private Limited**, designed to track the complete Request-for-Quote (RFQ) lifecycle — from buyer request to final purchase order — across brands, sellers, and clients.

The dashboard is built on a **star schema data model** (fact + dimension tables) covering the full RFQ → Seller Quote → Buyer Quote → Buyer PO → Seller PO funnel, exposed through interactive, filterable visuals built entirely with DAX and Power Query.

---

## 🎯 Business Problem

Procurement teams raise thousands of RFQs every month, but the journey from *"request sent"* to *"PO raised"* passes through multiple stages, with drop-offs and pricing changes at every step. Teams need to answer questions like:

- Which RFQs never received a seller quote?
- Where is margin being lost between the seller's price and the buyer's price?
- Which brands, sellers, and clients are driving — or dragging — conversion?
- Is a given request stuck, pending, or fully converted?

This dashboard answers all of the above in a single, self-service Power BI report — no manual pivot tables, no static spreadsheets.

---

## 🖼️ Dashboard Preview

| RFQ Analysis | Top 50 Brand Summary | Brand Analysis |
|---|---|---|
| ![RFQ Analysis](Screenshots/RFQ%20Analysis.png) | ![Top 50 Brand Summary](Screenshots/Top%2050%20Brands%20Summary.png) | ![Brand Analysis](Screenshots/Brand%20Analysis.png) |

| Seller Analysis | Client Analysis | |
|---|---|---|
| ![Seller Analysis](Screenshots/Seller%20Analysis.png) | ![Client Analysis](Screenshots/Client%20Analysis.png) | |

---

## ✨ Key Features

- **Executive KPI Header** — Total RFQ, Seller Quote, Buyer Quote, Buyer PO, and Seller PO counts pinned across every page, with global Month, Converted/Pending, and entity filters (Clients, Sellers, Brands)
- **Unquoted Request Tracker** — a dedicated donut KPI surfacing RFQs sitting with *no seller response*, a direct lost-opportunity signal
- **Conversion Rate (RFQ & Value)** — dual conversion metrics distinguishing "how many requests converted" from "how much value converted," since the two often diverge
- **Top 50 Brand Summary** — combo chart ranking brands by Buyer Quote Amount against Amount Conversion %, backed by a full drill table (Total RFQ, Buyer Quote/PO Conversion %, BQuote-to-BPO Amount Conversion %)
- **Brand / Seller / Client Analysis Pages** — each with its own funnel breakdown, ranked contribution chart, and cross-filtering Buyer Req No slicer
- **Price Flow View (Row-Level Audit Trail)** — a drill-down table exposing List Price → Seller Quote Unit Price → Buyer Quote Unit Price → Buyer PO Unit Price → Seller PO Unit Price with Gross Margin % per line item
- Fully interactive: every visual cross-filters the report on click, with Month, Converted/Pending, and entity slicers applied globally

---

## 🧮 Data Model

Built on a **star schema** — a central **Dimension Table** (conformed dimension for brand, client, date, category) connected to four transactional fact tables: **Seller Quote**, **Buyer Quote**, **Buyer PO**, and **Seller PO**, chained together via `buyerReqItemId` → `sellerReqItemId` → `buyerQuoteItemId` → `buyerPOItemId`.

All visuals anchor on Dimension table columns for row context (Brand, Client, Seller, Date), with every KPI — RFQ Count, Conversion Rate, Gross Margin %, Buyer/Seller Unit Price — built as a DAX measure. This keeps the model consistent despite each fact table sitting at a different stage of the funnel and prevents many-to-one fan-out from distorting the numbers.

📄 See [Measures](DAX/Measures.md) and [Calculated Columns](DAX/Calculated%20Columns.md) for full DAX documentation.

---

## 🔧 Key DAX Techniques Used

- **Winning-price selection** — `MINX` + `VAR/RETURN` patterns to resolve the lowest qualifying seller price per RFQ line item
- **Conversion Rate measures (RFQ & Value)** — `DIVIDE`-based measures with safe zero-handling, split between count-based and amount-based conversion
- **Gross Margin %** — a measure architecture that separates genuine zero-cost data quality issues from real margin calculations, rather than letting bad data distort the KPI
- **Filter-context-safe funnel measures** — `FILTER(VALUES(...))` and `REMOVEFILTERS()` used across the Seller Quote → Buyer Quote → Buyer PO → Seller PO chain to keep every visual anchored to the Dimension table regardless of which fact table drives it

---

## 🛠️ Tools & Skills

`Power BI` · `DAX` · `Power Query` · `Star Schema Data Modeling` · `Row-Level Security (RLS)`

---

## 💡 Skills Demonstrated

- Data Modeling (Star Schema)
- DAX
- Filter Context
- Power Query
- Funnel / Conversion Analysis
- KPI Design
- Interactive Dashboard Development
- Procurement Analytics

---

## 👤 Contact

**Aditya Singh**

- LinkedIn: *(www.linkedin.com/in/aditya-singhs)*
