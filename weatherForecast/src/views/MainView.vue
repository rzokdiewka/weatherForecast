<script setup lang="ts">
import TownSearch from '@/components/TownSearch.vue'
import Forecast, { type WeatherData } from '@/components/ForecastFetcher.vue'
import Charts from '@/components/ChartsDrawer.vue'
import { reactive, ref } from 'vue'

const refetch = ref<boolean>(false)
const coordinates = reactive({ lon: null, lat: null, name: null })
const forecastData = reactive({
  date: [] as string[],
  temperature: [] as number[],
  pressure: [] as number[],
  precipitation: [] as string[],
  cloudCover: [] as number[],
  wind: [] as number[]
})
const getCoordinates = (coords) => {
  coordinates.lon = coords.lon.value
  coordinates.lat = coords.lat.value
  coordinates.name = coords.name.value
}
const refreshForecast = (refresh: boolean) => {
  refetch.value = refresh
}
const getForecastData = (fd: WeatherData) => {
  forecastData.date = fd.hourly.time
  forecastData.temperature = fd.hourly.temperature2m
  forecastData.precipitation = fd.hourly.precipitation
  forecastData.pressure = fd.hourly.surfacePressure
  forecastData.cloudCover = fd.hourly.cloudCover
  forecastData.wind = fd.hourly.windSpeed10m
}
</script>

<template>
  <town-search @update-coordinates="getCoordinates" @refresh="refreshForecast" />

  <forecast
    :town="coordinates.name"
    :lon="coordinates.lon"
    :lat="coordinates.lat"
    :refetch="refetch"
    @update-forecast="getForecastData"
  />
  <div id="chart-container"     v-if="coordinates.name"
        style="width: 1000px; height: 600px">
    <charts
      :date="forecastData.date"
      :temperature="forecastData.temperature"
      :pressure="forecastData.pressure"
      :precipitation="forecastData.precipitation"
      :cloudCover="forecastData.cloudCover"
      :wind="forecastData.wind"
    />
  </div>
</template>
