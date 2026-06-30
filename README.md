# Analytics Dashboard

A single-page analytics dashboard showing monthly business metrics — think Shopify admin or a simple Google Analytics view. Built with Vue 3, TypeScript, Vuetify 3, and Chart.js.

## Features

- **Summary cards** for revenue, visitors, conversion rate, and orders, each with an up/down/flat indicator showing change vs the previous month.
- **Three charts**: a monthly revenue bar chart, a visitors-over-time line chart, and a full-width conversion-rate area chart.
- **Month picker** in the app bar that filters everything. Defaults to "All" (full year); selecting a month switches cards to that month's numbers and highlights it in the charts.
- **Click-to-filter charts**: click a bar or point to select that month; click it again to return to "All".
- **Dark theme** by default, with a cohesive (non-rainbow) color palette and shared custom tooltips.
- **Responsive layout** — cards stack on small screens.

See [PLAN.md](PLAN.md) for the full project brief and design decisions.

## Tech stack

- Vue 3 + TypeScript + Vuetify 3
- Chart.js via `vue-chartjs`
- `@mdi/font` for Material Design Icons
- `vue-router` (single `/` route)
- Vite for build and dev tooling
- Fake data from a local JSON file (`src/data/metrics.json`) — no API calls

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

## Getting started

```sh
npm install
npm run dev      # start the dev server
npm run build    # type-check and build for production
npm run preview  # preview the production build
```

Learn more about the recommended setup and IDE support in the [Vue Docs TypeScript Guide](https://vuejs.org/guide/typescript/overview.html#project-setup).
