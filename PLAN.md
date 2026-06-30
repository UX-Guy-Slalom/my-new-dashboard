# My Dashboard - Project Brief

> Status: **Implemented.** This document reflects the current build, including
> decisions and refinements made since the original brief. Changes from the
> initial plan are called out as **Evolved:**.

## What is this?
A single-page analytics dashboard showing monthly business metrics. Think Shopify admin or a simple Google Analytics view.

## Data
A fake dataset lives in `src/data/metrics.json`.
12 months of data (Jan–Dec 2025), each month containing:
- `revenue` (dollar amount, trending upward with some variation)
- `visitors` (number, seasonal pattern - higher in summer)
- `conversions` (percentage, fluctuates between ~2.5–4.7%)
- `orders` (number, correlates loosely with visitors)

The JSON is imported directly into the view (no fetch / no API calls).

## Layout (Vuetify)
- `v-app-bar` at the top with the dashboard title ("Analytics Dashboard") and a month picker (`v-select`).
- The month picker defaults to **"All"** (full year).
- When a specific month is selected, the summary cards switch to that month's
  numbers and the charts highlight that month. When "All" is selected, cards show
  yearly totals/averages and charts show all 12 months.
- Below the app bar: a row of 4 summary cards (`MetricCard` built on `v-card`)
  for revenue, visitors, conversion rate, and orders.
- Below the cards: a row of 2 charts
    - Left: Bar chart of monthly revenue
    - Right: Line chart of visitors over time
- Below that: one full-width area chart (filled line) showing the conversion-rate trend.
- Uses `v-container`, `v-row`, `v-col` for the responsive grid.

## Interactions
- The month picker filters everything: summary cards show the selected month's
  numbers; charts highlight the selected month (active bar/point at full color,
  the rest dimmed).
- When "All" is selected, cards show yearly totals (revenue, visitors, orders)
  and the average for conversion rate; charts show all 12 months at full color.
- Cards show an up/down/flat arrow plus color (green/red) indicating change vs the
  **previous month**. When "All" is selected, the card footer reads "Jan – Dec 2025"
  instead of a delta.
- **Evolved — click-to-filter charts:** clicking a bar or point selects that month
  (same as using the picker). Clicking the already-selected month toggles back to "All".

## Style
- Dark theme by default (Vuetify dark theme, set in `main.ts`).
- Clean, minimal, lots of whitespace; flat/bordered cards with rounded corners.
- Cohesive (non-rainbow) chart palette:
    - Revenue / blue `#5B8DEF`
    - Visitors / teal `#4EC9A8`
    - Conversions / orange `#E8895A`
    - Orders / purple `#9D77DB`
- Custom dark tooltips and subtle grid lines shared across charts.
- Mobile responsive — cards stack on small screens (`cols="12" sm="6" lg="3"`).

## Tech
- Vue 3 + TypeScript + Vuetify 3
- Chart.js via `vue-chartjs` for all charts
- `@mdi/font` for Material Design Icons
- Fake data from a local JSON file (no API calls)
- **Evolved — routing:** although a single page, the app is wired through
  `vue-router` with one route (`/` → `HomeView`). `App.vue` renders a
  `<router-view />`, leaving room to add pages later without restructuring.

## Project structure
```
src/
  App.vue                   # v-app shell + <router-view />
  main.ts                   # Vuetify (dark) + router bootstrap
  router/index.ts           # single "home" route
  views/HomeView.vue        # app bar, cards, and all three charts
  components/MetricCard.vue  # reusable summary card with delta indicator
  data/metrics.json         # 12 months of fake metrics
```
