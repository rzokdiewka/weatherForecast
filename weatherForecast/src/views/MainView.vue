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
const coordinates = reactive({ lon: undefined, lat: undefined, name: undefined })
const forecastData = reactive<ForecastData>({
  date: [] as string[],
  temperature: [] as number[],
  pressure: [] as number[],
  precipitation: [] as string[],
  cloudCover: [] as number[],
  wind: [] as number[]
})
const getCoordinates = (coords: any) => {
  coordinates.lon = coords.lon.value
  coordinates.lat = coords.lat.value
  coordinates.name = coords.name.value
}
const refreshForecast = (refresh: boolean) => {
  refetch.value = refresh
}
const getForecastData = (fd: WeatherData) => {
  forecastData.date = fd.time
  forecastData.temperature = fd.temperature2m
  forecastData.precipitation = fd.precipitation
  forecastData.pressure = fd.surfacePressure
  forecastData.cloudCover = fd.cloudCover
  forecastData.wind = fd.windSpeed10m
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
  <div v-if="coordinates.name">

    <div id="chart-container"  style="width: 1000px; height: 600px">
      <ForecastCharts :forecastData="forecastData" />
    </div>
    <ForecastTable  :forecastData="forecastData" />
  </div>

</template>
