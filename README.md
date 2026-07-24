# 📊 RFQ Analysis — Procurement Funnel Intelligence Dashboard

**A Power BI solution tracking the complete Request-for-Quote (RFQ) lifecycle — from buyer request to final purchase order — across brands, sellers, and clients.**

> Built for real-world procurement operations at **Procmart Pvt. Ltd.**, this dashboard suite gives category and sourcing teams a single source of truth for conversion rates, pricing gaps, and vendor performance across thousands of monthly transactions.

---

## 🎯 Business Problem

Procurement teams raise thousands of RFQs every month, but the journey from *"request sent"* to *"PO raised"* passes through multiple stages — Seller Quote → Buyer Quote → Buyer PO → Seller PO — with drop-offs and pricing changes at every step. Without a unified view, teams couldn't answer basic questions like:

- Which RFQs never got a seller quote?
- Where is margin being lost between the seller's price and the buyer's price?
- Which brands, sellers, and clients are driving (or dragging) conversion?
- Is a request stuck, pending, or fully converted?

This project consolidates that entire funnel into five interconnected dashboards, letting stakeholders slice by month, brand, client, seller, or individual RFQ number in seconds.

---

## 🖥️ Dashboard Preview

*(Screenshots available in [`/screenshots`](./screenshots) — data shown is from live company operations; figures are shared for portfolio purposes only.)*

### 1. RFQ Analysis — Executive Overview
Category-wise and state-wise RFQ → Quote → PO funnel, conversion trend line, and top clients/brands by volume.

![RFQ Analysis](./screenshots/RFQ_Analysis.png)

### 2. Top 50 Brand Summary
Deep dive into the highest-volume brands with buyer quote conversion %, PO conversion %, and quote-to-PO amount conversion — the KPIs category managers check weekly.

![Top 50 Brand Summary](./screenshots/Top_50_Brands_Summary.png)

### 3. Brand Analysis
Brand-level breakdown of RFQ → Seller Quote → Buyer Quote → PO funnel with an **unquoted-request counter** that instantly flags demand no seller has responded to.

![Brand Analysis](./screenshots/Brand_Analysis.png)

### 4. Seller Analysis
Seller-wise conversion rate and buyer quote amount, with a row-level **Price Flow View** exposing list price, discount %, and gross margin % for every line item.

![Seller Analysis](./screenshots/Seller_Analysis.png)

### 5. Client Analysis
Client-wise buyer quote amount ranking alongside product- and brand-level gross margin trends, so account managers can see exactly where a client's spend is going.

![Client Analysis](./screenshots/Client_Analysis.png)

---

## 🧠 What This Dashboard Actually Solves

| Metric | Why It Matters |
|---|---|
| **Conversion Rate (RFQ & Value)** | Measures how much of raised demand actually turns into revenue |
| **Unquoted Requests** | Flags RFQs sitting with *no seller response* — direct lost-opportunity signal |
| **Gross Margin %** | Line-item level margin between seller quote price and buyer quote price |
| **Price Flow View** | Full audit trail: List Price → Seller Quote → Buyer Quote → Buyer PO → Seller PO, per item |
| **Buyer Quote → PO Conversion %** | Identifies where quoted deals stall before becoming a PO |

---

## 🏗️ Data Model & DAX

The dashboard runs on a **normalized star schema** (fact + dimension tables) rather than a single flat table, purpose-built to handle multi-stage many-to-one relationships (one RFQ item can carry multiple seller quotes, multiple buyer quotes, etc.) without fan-out breaking the numbers.

**Core tables:** `Dimension Table` (conformed dimension for brand/client/date) · `Seller Quote` · `Buyer Quote` · `Buyer PO` · `Seller PO` · `Calendar` · `Categories`

Key DAX techniques used (full measures documented in [`/dax`](./dax)):
- **Winning-price selection** using `MINX` + `VAR/RETURN` patterns to resolve the lowest qualifying seller price per item
- **Conversion rate measures** (RFQ count-based and Amount-based) using `DIVIDE` with safe zero-handling
- **Gross Margin %** measures that separate genuine zero-cost data issues from real margin calculations
- Filter-context-safe measures (`REMOVEFILTERS`, `FILTER(VALUES(...))`) to keep visuals anchored to the Dimension table regardless of which fact table drives a given chart

---

## 🛠️ Tech Stack

- **Power BI Desktop** — data modeling, DAX, report design
- **Power Query (M)** — data cleaning, shaping, and transformation
- **DAX** — calculated columns, measures, KPIs
- **Star Schema Modeling** — dimensional design for scalable, fan-out-safe analytics
- **Row-Level Security (RLS)** — role-based data access

---

## 📈 Skills Demonstrated

- End-to-end BI development: raw data → data model → DAX → interactive report
- Funnel/conversion analysis across a multi-stage transactional process
- Star schema design for real transactional (non-trivial many-to-one) data
- Business-first KPI design — built from what procurement stakeholders actually need to act on
- Dashboard UX: cross-page filter panels, drill-through-ready visuals, row-level detail views

---

## 🔒 A Note on Data

This dashboard was built on live operational data from Procmart Pvt. Ltd. The `.pbix` file and raw dataset are **not included** in this repository for confidentiality reasons. This repo shares the **methodology, data model, DAX logic, and dashboard screenshots** to demonstrate the analytical approach and technical build.

---

## 👤 About Me

**Aditya** — Executive, Indirect Operations at Procmart Pvt. Ltd. | Power BI Developer

I build production procurement analytics dashboards end-to-end — from data modeling to DAX to final report design — for a live business, not just tutorial datasets.

📫 Feel free to connect or reach out if you'd like to discuss the approach behind this project.

---

⭐ If you found this project useful or interesting, consider giving it a star!
