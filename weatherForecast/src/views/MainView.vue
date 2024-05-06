<script setup lang="ts">
import TownSearch from '@/components/TownSearch.vue'
import Forecast, { type WeatherData } from '@/components/ForecastFetcher.vue'
import Charts from '@/components/ChartsDrawer.vue'
import { reactive, watch } from 'vue'

const coordinates = reactive({ lon: null, lat: null })
const forecastData = reactive({
  date: [] as string[],
  temperature: [] as number[],
  pressure: [] as number[],
  precipitation: [] as string[],
  skyClearness: [] as number[],
  wind: [] as number[]
})
const getCoordinates = (coords) => {
  coordinates.lon = coords.lon.value
  coordinates.lat = coords.lat.value

  console.log(coordinates)
}

const getForecastData = (fd: WeatherData) => {
  forecastData.date = fd.hourly.time
  forecastData.temperature = fd.hourly.temperature2m
  forecastData.precipitation = fd.hourly.precipitation
  forecastData.pressure = fd.hourly.surfacePressure
  forecastData.skyClearness = fd.hourly.skyClearness
  forecastData.wind = fd.hourly.windSpeed10m
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
      :skyClearness="forecastData.skyClearness"
      :wind="forecastData.wind"
    />
  </div>
</template>
