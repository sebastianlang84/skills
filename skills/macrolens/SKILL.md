---
name: macrolens
description: API reference for the MacroLens service. Use when an agent needs to fetch market/macro data, retrieve available tickers, or read dashboard signals from the MacroLens endpoints.
---

# MacroLens API

Base URL: `http://127.0.0.1:3001`

## Endpoints

### GET /api/catalog

Returns all available series as a JSON array.

```
GET http://127.0.0.1:3001/api/catalog
```

Each entry:

```json
{
  "key": "xlk",
  "label": "Technology (XLK)",
  "shortLabel": "XLK – Tech",
  "source": "yahoo" | "fred",
  "unit": "usd" | "index" | "percent" | "thousand_persons",
  "description": "...",
  "providerId": "XLK",
  "lookbackYears": 3,
  "color": "#6366f1",
  "proxyNote": "..."   // optional
}
```

Use `key` to reference a series in other calls. Use `source` + `providerId` to know the upstream origin.

---

### GET /api/dashboard

Returns aggregated macro dashboard data including derived signals.

```
GET http://127.0.0.1:3001/api/dashboard
```

Contains current values, trends, and signal assessments across all macro series (Fed Funds, CPI, yields, spreads, etc.).

---

## Available series (summary)

**Assets (Yahoo Finance)**
`sp500`, `nasdaq100`, `sp500_equal_weight`, `gold`, `bitcoin`, `oil`

**Sector ETFs — SPDR (Yahoo Finance)**
`xlk`, `xlv`, `xlf`, `xli`, `xly`, `xlp`, `xle`, `xlu`, `xlb`, `xlre`, `xlc`

**Macro (FRED)**
`fedfunds`, `cpi`, `unrate`, `dgs10`, `dgs2`, `ig_oas`, `hy_oas`, `payrolls`, `breakeven_5y`, `pce`, `ppi`, `gasoline`
