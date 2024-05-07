<script setup lang="ts">
import type { ForecastData } from '@/views/MainView.vue'

const props = defineProps<{
  forecastData: ForecastData
}>()
</script>

<template>
  <div>
    <table>
      <thead>
        <tr>
          <th>Date, hour</th>
          <th>Temperature</th>
          <th>Precipitation</th>
          <th>Cloud cover</th>
          <th>Pressure</th>
          <th>Wind Speed</th>
        </tr>
      </thead>
      <tbody id="example-1">
        <tr v-for="(item, index) in props.forecastData.date" :key="index">
          <td>
            {{ (item) }}
          </td>
          <td>{{ props.forecastData.temperature[index] }} &degC</td>
          <td>{{ props.forecastData.precipitation[index] }} ml</td>
          <td>{{ props.forecastData.cloudCover[index] }} %</td>
          <td>{{ props.forecastData.pressure[index] }} hPa</td>
          <td>{{props.forecastData.w}}{{ props.forecastData.wind[index] }} km/h</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped></style>
