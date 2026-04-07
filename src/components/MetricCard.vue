<script setup lang="ts">
defineProps<{
  title: string
  value: string
  icon: string
  delta: number | null
  color: string
}>()
</script>

<template>
  <v-card variant="flat" border rounded="lg" height="100%">
    <v-card-text class="pa-5">
      <div class="d-flex align-center justify-space-between mb-4">
        <span class="text-overline text-medium-emphasis letter-spacing-wider">{{ title }}</span>
        <v-icon :color="color" size="20">{{ icon }}</v-icon>
      </div>

      <div class="text-h5 font-weight-bold mb-2">{{ value }}</div>

      <div v-if="delta !== null" class="d-flex align-center text-caption">
        <v-icon
          :color="delta > 0 ? 'success' : delta < 0 ? 'error' : 'medium-emphasis'"
          size="16"
          class="mr-1"
        >
          {{ delta > 0 ? 'mdi-trending-up' : delta < 0 ? 'mdi-trending-down' : 'mdi-minus' }}
        </v-icon>
        <span :class="delta > 0 ? 'text-success' : delta < 0 ? 'text-error' : 'text-medium-emphasis'">
          {{ delta > 0 ? '+' : '' }}{{ delta }}% vs prev month
        </span>
      </div>

      <div v-else class="text-caption text-medium-emphasis">Jan – Dec 2025</div>
    </v-card-text>
  </v-card>
</template>
