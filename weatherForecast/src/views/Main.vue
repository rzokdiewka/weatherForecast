<script setup lang="ts">
import TownSearch from '@/components/TownSearch.vue'
import Forecast from '@/components/Forecast.vue'
import Charts from '@/components/Charts.vue'
import { reactive, ref, watch } from 'vue'

const coordinates = reactive({ lon: null, lat: null })
const forecastData = reactive({
  date: Array<String>,
  temperature: Array<Number>,
  pressure: Array<Number>,
  precipitation: Array<Number>,
  cloud: Array<Number>,
  wind: Array<Number>
})
const getCoordinates = (coords) => {
  coordinates.lon = coords.lon.value
  coordinates.lat = coords.lat.value

  console.log(coordinates)
}

const getForecastData = (fd) => {
  forecastData.date = fd.hourly.time
  forecastData.temperature = Array.from(fd.hourly.temperature2m, (x) => Math.round(x))
  forecastData.pressure = Array.from(fd.hourly.surfacePressure, (x) => Math.round(x))
  forecastData.precipitation = Array.from(fd.hourly.precipitation, (x) => x.toFixed(2))
  forecastData.cloud = Array.from(fd.hourly.cloudCover, (x) => 100 - Math.round(x))
  forecastData.wind = Array.from(fd.hourly.windSpeed10m, (x) => Math.round(x))
  console.log('forec', forecastData)
}
watch(
  () => forecastData,
  () => console.log('forec watch', forecastData)
)
</script>

<template>
  <town-search @update-coordinates="getCoordinates" />
  <forecast :lon="coordinates.lon" :lat="coordinates.lat" @update-forecast="getForecastData" />
  <div id="chart-container" style="width: 1000px; height: 600px">
    <charts
      :date="forecastData.date"
      :temperature="forecastData.temperature"
      :pressure="forecastData.pressure"
      :precipitation="forecastData.precipitation"
      :cloud="forecastData.cloud"
      :wind="forecastData.wind"
    />
  </div>
</template>
