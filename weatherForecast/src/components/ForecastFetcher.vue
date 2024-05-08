<script setup lang="ts">
import { fetchWeatherApi } from 'openmeteo'
import { WeatherApiResponse } from '@openmeteo/sdk/weather-api-response'

import { ref, watch } from 'vue'

const emit = defineEmits(['updateForecast'])

const props = defineProps<{
  town?: string
  lon?: number
  lat?: number
  refetch: boolean
}>()

export interface WeatherData {
  time: Array<string>
  temperature2m: Array<number>
  precipitation: Array<string>
  surfacePressure: Array<number>
  cloudCover: Array<number>
  windSpeed10m: Array<number>
}

const forecastData = ref()
const params = {
  latitude: props.lat,
  longitude: props.lon,
  current: [
    'temperature_2m',
    'is_day',
    'precipitation',
    'cloud_cover',
    'surface_pressure',
    'wind_speed_10m',
    'wind_direction_10m'
  ],
  hourly: ['temperature_2m', 'precipitation', 'surface_pressure', 'cloud_cover', 'wind_speed_10m'],
  timezone: 'auto'
}
const url = 'https://api.open-meteo.com/v1/forecast'

const timezone = ref()

const data = async () => {
  const responses = await fetchWeatherApi(url, params)

  // Process first location. Add a for-loop for multiple locations or weather models
  forecastData.value = responses[0]
}

// console.log(response)
const transformData = (response: WeatherApiResponse): WeatherData => {
  // Helper function to form time ranges
  const range = (start: number, stop: number, step: number) =>
    Array.from({ length: (stop - start) / step }, (_, i) => start + i * step)

  // Attributes for timezone and location
  const utcOffsetSeconds = response.utcOffsetSeconds()
  const currentZoneOffsetSeconds = utcOffsetSeconds + new Date().getTimezoneOffset() * 60
  timezone.value = response.timezoneAbbreviation()
  const hourly = response.hourly()!
  console.log('utc', utcOffsetSeconds, currentZoneOffsetSeconds)
  // Note: The order of weather variables in the URL query and the indices below need to match!

  return {
    time: range(Number(hourly.time()), Number(hourly.timeEnd()), hourly.interval()).map((t) => {
      const date = new Date((t + currentZoneOffsetSeconds) * 1000)
      // const dateString = date.toDateString()
      const weekDay = date.toDateString().split(' ')[0]
      if (date.getHours() !== 0)
        return (
          weekDay +
          ', ' +
          date.toLocaleString(navigator.language, { hour: '2-digit', minute: '2-digit' })
        )
      else
        return (
          weekDay +
          ' ' +
          date.toLocaleString(navigator.language, { month: 'short', day: 'numeric' })
        )
    }),
    temperature2m: Array.from(hourly.variables(0)!.valuesArray()!, (x: number) => Math.round(x)),
    precipitation: Array.from(hourly.variables(1)!.valuesArray()!, (x) => x.toFixed(2)),
    surfacePressure: Array.from(hourly.variables(2)!.valuesArray()!, (x) => Math.round(x)),
    cloudCover: Array.from(hourly.variables(3)!.valuesArray()!, (x) => Math.round(x)),
    windSpeed10m: Array.from(hourly.variables(4)!.valuesArray()!, (x) => Math.round(x))
  }
}

watch(
  () => [props.refetch, props.lat, props.lon],
  () => {
    console.log('fetch', props)
    params.latitude = props.lat
    params.longitude = props.lon
    data()
  }
)

watch(
  () => forecastData.value,
  () => {
    const data: WeatherData = transformData(forecastData.value)
    emit('updateForecast', data)
  }
)
</script>

<template>
  <span v-if="town">{{ town }}, {{ timezone }}</span>
</template>

<style scoped></style>
