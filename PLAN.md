# My Dashboard - Project Brief

## What is this? 
A single-page analytics dashboard showing monthly business metrics. Think Shopify admin or a simple Google Analytics view.

## Data
Generate a fake dataset as a JSON file (src/data/metrics.json). 
12 months of data (Jan-Dec 2025), each month containing:
- revenuew (dollar amount, trending upward with some variation)
- visitors (number, seasonal pattern - higher in summer)
- conversions (percentage, fluctuates between 2-5%)
- orders (number, correlates loosely with visitors)

## Layout (Vuetify)
- v-app-bar at the top with the dashboard title and a monthg picker
- The month picker should default to showing ALL months
- When a a specific month is selected, all cards and charts filter to that month. When "All" is selected, show the full year.
- Below the app bar: a row of 4 summary cards (v-card) showing the key metrics - revenue, visitors, conversions, orders
- Below the cards: a  row of 2 charts
    - Left: Bar chart showing monthly revenue 
    - Right: Line chart showing visitors over time
- Below that: one full-width area chart showing conversions trend
- Use v-container, v-row, v-col for responsive grid layout

## Interactions
- Month picker in the app bar filters EVERYTHING - summary cards show that month's numbers, charts highlight or filter to that month
- When "All" is selected, summary cards show yearly totals/averages and charts show all 12 months
- Cards should show a asmall up/down arrow or color indicating change from the previous month

## Style 
- Dark theme by default (vuetify dark theme)
- Clean, minimal, lots of whitespace
- Charts should use a cohesive color palette - not a rainbow
- Mobile responsive - cards stack on small screens

## Tech 
- Vue 3 + Typescript + vuetify 3
- Chart.js via vue-chartjs for all charts
- Fake data from a local JSON file (no API calls)
- Single page - no routing needed for this app