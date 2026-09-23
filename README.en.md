[🇧🇷 Português](README.md) | 🇺🇸 English

# 📊 Finance Dashboard — Real-time cash flow, with end-to-end automation

An interactive financial dashboard built as **a single self-contained HTML file** (no build step, no runtime external dependencies), fed by an automation pipeline that reads data straight from a financial ERP, consolidates it, and publishes itself — with zero manual steps.

**[🔗 Live demo](https://andrescultori.github.io/dashboard-financeiro/)**
Demo password: `demo2026`

> ⚠️ **All data in this repository is fictional**, randomly generated for demonstration purposes. This project is an adaptation, with the data replaced, of a real production system built for a nonprofit educational institution.

![Annual dashboard](screenshots/dashboard-anual.png)

---

## The original problem

The organization used to close the books manually every month: export reports from the accounting system, consolidate them into spreadsheets via Power Query, and build a dashboard in Power BI — a process that took hours and depended on someone remembering to do it every week.

## The solution

A pipeline that runs itself, once a week:

```
Financial ERP (via API)
        ↓
Consolidated Excel file (SharePoint)
        ↓
n8n (self-hosted, Docker) — reads, consolidates and aggregates the data
        ↓
HTML dashboard (dynamically generated, with the data embedded)
        ↓
Automatically published (deploy via API)
```

Nobody needs to open a spreadsheet, run a script, or click "refresh." The dashboard the board of directors opens on Monday morning already reflects the previous week.

## The dashboard itself

- **Annual view**: credit, debit and accumulated balance KPIs, with a background area chart showing the recent trend
- **Mixed monthly chart**: inflow/outflow columns + accumulated balance line, with values displayed directly on the chart
- **Monthly table** next to the chart, with visual highlighting for positive/negative balance
- **Top 10 categories** for credit and debit, **clickable** — clicking a category filters the whole dashboard by it (KPIs, chart and table recalculate instantly)
- **Dedicated monthly report**: daily cash flow, KPIs for the selected month, and historical accumulated balance
- **Authentication** — in the production version, real corporate login (Microsoft Entra ID / MSAL.js), restricted by email; in this demo, a simple password illustrates the technique without requiring a real Microsoft account

![Monthly report](screenshots/relatorio-mensal.png)

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | Vanilla HTML + JavaScript (no framework), Chart.js |
| Authentication (production) | Microsoft Entra ID via MSAL.js, with email restriction enforced in Entra ID itself |
| Automation | [n8n](https://n8n.io) (self-hosted, Docker) |
| Data source | Financial ERP API → Excel (Microsoft Graph API) |
| Publishing | Automatic deploy via API (Netlify) |
| Design | Custom color/typography system (dark theme, Space Grotesk + Inter) |

**A deliberate technical decision:** the dashboard is a single `.html` file, with no build step, no `node_modules`, no frontend framework. Libraries (Chart.js, and MSAL.js in the production version) are **embedded directly in the file** as text, instead of loaded via CDN — this makes the file work standalone, even offline, and avoids any network dependency at runtime.

## The automation pipeline (`/automation`)

- [`n8n-workflow.json`](automation/n8n-workflow.json) — the full publishing workflow: authenticates via OAuth2 client credentials, lists files in a SharePoint folder, picks the most recent one per month, extracts the Excel rows, consolidates them into JSON, injects it into an HTML template, and publishes via API
- [`docker-compose.example.yml`](automation/docker-compose.example.yml) — example configuration for running n8n self-hosted

The consolidation logic (grouping by month/day/category, computing the running balance) is embedded in the workflow as a code node — no external dependencies, just plain JavaScript.

## Running locally

Nothing needed besides a browser:

```bash
git clone <this-repository>
cd <folder>
open index.html   # or just double-click the file
```

Password: `demo2026`

---

*Project adapted from a production system. Data, names and identifiers have been replaced with fictional values for this public demonstration.*

Built by [André Scultori](https://github.com/andrescultori)  ·  © 2026  ·  [GitHub](https://github.com/andrescultori/dashboard-financeiro)
