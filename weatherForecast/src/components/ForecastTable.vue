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
          <th>Date</th>
          <th>Hour</th>
          <th>Temperature</th>
          <th>Precipitation</th>
          <th>Cloud cover</th>
          <th>Pressure</th>
          <th>Wind Speed</th>
        </tr>
      </thead>
      <tbody id="example-1">
        <tr v-for="(item, index) in props.forecastData.date" :key="index">
          <td rowspan="24" v-if="index % 24 == 0">
            {{ props.forecastData.date[index % 24].split(', ')[0] }}
          </td>
          <td v-if="index % 24 == 0">00:00</td>
          <td v-else>{{ item.split(' ')[1] }}</td>
          <td>{{ props.forecastData.temperature[index] }} &deg;C</td>
          <td>{{ props.forecastData.precipitation[index] }} mm</td>
          <td>{{ props.forecastData.cloudCover[index] }} %</td>
          <td>{{ props.forecastData.pressure[index] }} hPa</td>
          <td>{{ props.forecastData.wind[index] }} km/h</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped></style>
