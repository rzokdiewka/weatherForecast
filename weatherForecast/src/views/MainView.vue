<script setup lang="ts">
import TownSearch from '@/components/TownSearch.vue'
import ForecastFetcher, { type WeatherData } from '@/components/ForecastFetcher.vue'
import ForecastCharts from '@/components/ForecastCharts.vue'
import ForecastTable from '@/components/ForecastTable.vue'
import { reactive, ref } from 'vue'

export interface ForecastData {
  date: string[]
  temperature: number[]
  pressure: number[]
  precipitation: string[]
  cloudCover: number[]
  wind: number[]
}
const refetch = ref<boolean>(false)
const coordinates = reactive({ lon: null, lat: null, name: null })
const forecastData = reactive<ForecastData>({
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

  <ForecastFetcher
    :town="coordinates.name"
    :lon="coordinates.lon"
    :lat="coordinates.lat"
    :refetch="refetch"
    @update-forecast="getForecastData"
  />
  <div id="chart-container" v-if="coordinates.name" style="width: 1000px; height: 600px">
    <ForecastCharts
      :date="forecastData.date"
      :temperature="forecastData.temperature"
      :pressure="forecastData.pressure"
      :precipitation="forecastData.precipitation"
      :cloudCover="forecastData.cloudCover"
      :wind="forecastData.wind"
    />
  </div>
  <ForecastTable :forecastData="forecastData" />

</template>
