<script setup lang="ts">
import { ref, computed } from 'vue'
import { Bar, Line } from 'vue-chartjs'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  LineElement,
  PointElement,
  CategoryScale,
  LinearScale,
  Filler,
} from 'chart.js'
import MetricCard from '../components/MetricCard.vue'
import metricsRaw from '../data/metrics.json'

ChartJS.register(
  Title, Tooltip, Legend,
  BarElement, LineElement, PointElement,
  CategoryScale, LinearScale, Filler,
)

interface MonthData {
  month: string
  revenue: number
  visitors: number
  conversions: number
  orders: number
}

const metrics: MonthData[] = metricsRaw

// ─── Month picker ────────────────────────────────────────────────────────────
const MONTH_ITEMS = ['All', ...metrics.map(d => d.month)]
const selectedMonth = ref('All')

const selectedIdx = computed(() =>
  selectedMonth.value === 'All'
    ? -1
    : metrics.findIndex(d => d.month === selectedMonth.value),
)

const prevMonthData = computed<MonthData | null>(() =>
  selectedIdx.value > 0 ? metrics[selectedIdx.value - 1] : null,
)

// ─── Summary values ───────────────────────────────────────────────────────────
const summaryRevenue = computed(() =>
  selectedIdx.value === -1
    ? metrics.reduce((s, d) => s + d.revenue, 0)
    : metrics[selectedIdx.value].revenue,
)
const summaryVisitors = computed(() =>
  selectedIdx.value === -1
    ? metrics.reduce((s, d) => s + d.visitors, 0)
    : metrics[selectedIdx.value].visitors,
)
const summaryConversions = computed(() =>
  selectedIdx.value === -1
    ? parseFloat((metrics.reduce((s, d) => s + d.conversions, 0) / metrics.length).toFixed(2))
    : metrics[selectedIdx.value].conversions,
)
const summaryOrders = computed(() =>
  selectedIdx.value === -1
    ? metrics.reduce((s, d) => s + d.orders, 0)
    : metrics[selectedIdx.value].orders,
)

// ─── Deltas ───────────────────────────────────────────────────────────────────
function pctDelta(curr: number, prev: number): number {
  return parseFloat((((curr - prev) / prev) * 100).toFixed(1))
}
const revenueDelta = computed(() =>
  prevMonthData.value ? pctDelta(summaryRevenue.value, prevMonthData.value.revenue) : null,
)
const visitorsDelta = computed(() =>
  prevMonthData.value ? pctDelta(summaryVisitors.value, prevMonthData.value.visitors) : null,
)
const conversionsDelta = computed(() =>
  prevMonthData.value ? pctDelta(summaryConversions.value, prevMonthData.value.conversions) : null,
)
const ordersDelta = computed(() =>
  prevMonthData.value ? pctDelta(summaryOrders.value, prevMonthData.value.orders) : null,
)

// ─── Formatters ───────────────────────────────────────────────────────────────
const fmtUSD = (n: number) => '$' + n.toLocaleString('en-US')
const fmtNum = (n: number) => n.toLocaleString('en-US')
const fmtPct = (n: number) => n + '%'

// ─── Chart palette ────────────────────────────────────────────────────────────
const C_BLUE   = '#5B8DEF'
const C_TEAL   = '#4EC9A8'
const C_ORANGE = '#E8895A'

function highlight(activeColor: string, dimColor: string): string[] {
  return metrics.map((_, i) =>
    selectedIdx.value === -1 || i === selectedIdx.value ? activeColor : dimColor,
  )
}
function pointRadii(normal: number, selected: number): number[] {
  return metrics.map((_, i) =>
    selectedIdx.value !== -1 && i === selectedIdx.value ? selected : normal,
  )
}

// ─── Shared chart style ───────────────────────────────────────────────────────
const tooltipDefaults = {
  backgroundColor: '#16161E',
  titleColor: '#CDD6F4',
  bodyColor: '#A6ADC8',
  borderColor: '#2A2A3C',
  borderWidth: 1,
  padding: 10,
  cornerRadius: 6,
}
const gridColor = 'rgba(255,255,255,0.06)'
const tickColor = '#6C7086'

// ─── Chart click handler ──────────────────────────────────────────────────────
function handleChartClick(_event: unknown, elements: { index: number }[]) {
  if (!elements.length) return
  const month = metrics[elements[0].index].month
  // Toggle off if clicking the already-selected month
  selectedMonth.value = selectedMonth.value === month ? 'All' : month
}

// ─── Revenue bar chart ────────────────────────────────────────────────────────
const revenueData = computed(() => ({
  labels: metrics.map(d => d.month),
  datasets: [{
    label: 'Revenue',
    data: metrics.map(d => d.revenue),
    backgroundColor: highlight(C_BLUE, 'rgba(91,141,239,0.18)'),
    borderRadius: 5,
    borderSkipped: false as const,
  }],
}))

const revenueOptions = {
  responsive: true,
  maintainAspectRatio: false,
  onClick: handleChartClick,
  plugins: {
    legend: { display: false },
    tooltip: {
      ...tooltipDefaults,
      callbacks: {
        label: (ctx: { parsed: { y: number } }) =>
          '  $' + ctx.parsed.y.toLocaleString('en-US'),
      },
    },
  },
  scales: {
    x: { grid: { color: gridColor }, ticks: { color: tickColor } },
    y: {
      grid: { color: gridColor },
      ticks: {
        color: tickColor,
        callback: (value: number | string) =>
          typeof value === 'number' ? '$' + Math.round(value / 1000) + 'k' : value,
      },
    },
  },
}

// ─── Visitors line chart ──────────────────────────────────────────────────────
const visitorsData = computed(() => ({
  labels: metrics.map(d => d.month),
  datasets: [{
    label: 'Visitors',
    data: metrics.map(d => d.visitors),
    borderColor: C_TEAL,
    backgroundColor: 'rgba(78,201,168,0.08)',
    pointBackgroundColor: highlight(C_TEAL, 'rgba(78,201,168,0.2)'),
    pointBorderColor: highlight(C_TEAL, 'rgba(78,201,168,0.2)'),
    pointRadius: pointRadii(3, 7),
    pointHoverRadius: 7,
    borderWidth: 2,
    tension: 0.4,
    fill: false,
  }],
}))

const visitorsOptions = {
  responsive: true,
  maintainAspectRatio: false,
  onClick: handleChartClick,
  interaction: { mode: 'index' as const, intersect: false },
  plugins: {
    legend: { display: false },
    tooltip: {
      ...tooltipDefaults,
      callbacks: {
        label: (ctx: { parsed: { y: number } }) =>
          '  ' + ctx.parsed.y.toLocaleString('en-US') + ' visitors',
      },
    },
  },
  scales: {
    x: { grid: { color: gridColor }, ticks: { color: tickColor } },
    y: {
      grid: { color: gridColor },
      ticks: {
        color: tickColor,
        callback: (value: number | string) =>
          typeof value === 'number' ? Math.round(value / 1000) + 'k' : value,
      },
    },
  },
}

// ─── Conversions area chart ───────────────────────────────────────────────────
const conversionsData = computed(() => ({
  labels: metrics.map(d => d.month),
  datasets: [{
    label: 'Conversion Rate',
    data: metrics.map(d => d.conversions),
    borderColor: C_ORANGE,
    backgroundColor: 'rgba(232,137,90,0.15)',
    pointBackgroundColor: highlight(C_ORANGE, 'rgba(232,137,90,0.2)'),
    pointBorderColor: highlight(C_ORANGE, 'rgba(232,137,90,0.2)'),
    pointRadius: pointRadii(3, 7),
    pointHoverRadius: 7,
    borderWidth: 2,
    tension: 0.4,
    fill: true,
  }],
}))

const conversionsOptions = {
  responsive: true,
  maintainAspectRatio: false,
  onClick: handleChartClick,
  interaction: { mode: 'index' as const, intersect: false },
  plugins: {
    legend: { display: false },
    tooltip: {
      ...tooltipDefaults,
      callbacks: {
        label: (ctx: { parsed: { y: number } }) =>
          '  ' + ctx.parsed.y + '%',
      },
    },
  },
  scales: {
    x: { grid: { color: gridColor }, ticks: { color: tickColor } },
    y: {
      grid: { color: gridColor },
      min: 0,
      max: 6,
      ticks: {
        color: tickColor,
        callback: (value: number | string) =>
          typeof value === 'number' ? value + '%' : value,
      },
    },
  },
}
</script>

<template>
  <!-- App bar with month picker -->
  <v-app-bar flat elevation="0" color="surface" border="b">
    <v-toolbar-title class="font-weight-bold">Analytics Dashboard</v-toolbar-title>
    <template #append>
      <v-select
        v-model="selectedMonth"
        :items="MONTH_ITEMS"
        density="compact"
        variant="outlined"
        hide-details
        style="min-width: 130px"
        class="mr-4"
      />
    </template>
  </v-app-bar>

  <v-main>
    <v-container fluid class="pa-6">

      <!-- Summary cards -->
      <v-row class="mb-4">
        <v-col cols="12" sm="6" lg="3">
          <MetricCard
            title="Revenue"
            :value="fmtUSD(summaryRevenue)"
            icon="mdi-currency-usd"
            :delta="revenueDelta"
            color="#5B8DEF"
          />
        </v-col>
        <v-col cols="12" sm="6" lg="3">
          <MetricCard
            title="Visitors"
            :value="fmtNum(summaryVisitors)"
            icon="mdi-account-multiple"
            :delta="visitorsDelta"
            color="#4EC9A8"
          />
        </v-col>
        <v-col cols="12" sm="6" lg="3">
          <MetricCard
            title="Conversion Rate"
            :value="fmtPct(summaryConversions)"
            icon="mdi-percent-outline"
            :delta="conversionsDelta"
            color="#E8895A"
          />
        </v-col>
        <v-col cols="12" sm="6" lg="3">
          <MetricCard
            title="Orders"
            :value="fmtNum(summaryOrders)"
            icon="mdi-package-variant-closed"
            :delta="ordersDelta"
            color="#9D77DB"
          />
        </v-col>
      </v-row>

      <!-- Revenue + Visitors row -->
      <v-row class="mb-4">
        <v-col cols="12" md="6">
          <v-card variant="flat" border rounded="lg">
            <v-card-title class="text-body-2 font-weight-medium text-medium-emphasis px-5 pt-5 pb-1">
              Monthly Revenue
            </v-card-title>
            <v-card-text class="pa-4 pt-2">
              <div style="position: relative; height: 280px; cursor: pointer">
                <!-- eslint-disable-next-line @typescript-eslint/no-explicit-any -->
                <Bar :data="(revenueData as any)" :options="(revenueOptions as any)" />
              </div>
            </v-card-text>
          </v-card>
        </v-col>
        <v-col cols="12" md="6">
          <v-card variant="flat" border rounded="lg">
            <v-card-title class="text-body-2 font-weight-medium text-medium-emphasis px-5 pt-5 pb-1">
              Visitors Over Time
            </v-card-title>
            <v-card-text class="pa-4 pt-2">
              <div style="position: relative; height: 280px; cursor: pointer">
                <!-- eslint-disable-next-line @typescript-eslint/no-explicit-any -->
                <Line :data="(visitorsData as any)" :options="(visitorsOptions as any)" />
              </div>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>

      <!-- Conversions area chart -->
      <v-row>
        <v-col cols="12">
          <v-card variant="flat" border rounded="lg">
            <v-card-title class="text-body-2 font-weight-medium text-medium-emphasis px-5 pt-5 pb-1">
              Conversion Rate Trend
            </v-card-title>
            <v-card-text class="pa-4 pt-2">
              <div style="position: relative; height: 240px; cursor: pointer">
                <!-- eslint-disable-next-line @typescript-eslint/no-explicit-any -->
                <Line :data="(conversionsData as any)" :options="(conversionsOptions as any)" />
              </div>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>

    </v-container>
  </v-main>
</template>
