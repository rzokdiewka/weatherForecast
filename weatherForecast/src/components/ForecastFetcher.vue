<script setup lang="ts">
import { fetchWeatherApi } from 'openmeteo'
import { WeatherApiResponse } from '@openmeteo/sdk/weather-api-response'

import { ref, watch } from 'vue'

const emit = defineEmits(['updateForecast'])

const props = defineProps({
  town: String,
  lon: Number,
  lat: Number,
  refetch: Boolean
})

export interface WeatherData {
  current: {
    time: Date
    temperature2m: number
    isDay: number
    precipitation: number
    cloudCover: number
    surfacePressure: number
    windSpeed10m: number
    windDirection10m: number
  }
  hourly: {
    time: Array<string>
    temperature2m: Array<number>
    precipitation: Array<string>
    surfacePressure: Array<number>
    cloudCover: Array<number>
    windSpeed10m: Array<number>
  }
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
  timezone.value = response.timezoneAbbreviation()
  const current = response.current()!
  const hourly = response.hourly()!

  // Note: The order of weather variables in the URL query and the indices below need to match!
  return {
    current: {
      time: new Date((Number(current.time()) + utcOffsetSeconds) * 1000),
      temperature2m: current.variables(0)!.value(),
      isDay: current.variables(1)!.value(),
      precipitation: current.variables(2)!.value(),
      cloudCover: current.variables(3)!.value(),
      surfacePressure: current.variables(4)!.value(),
      windSpeed10m: current.variables(5)!.value(),
      windDirection10m: current.variables(6)!.value()
    },
    hourly: {
      time: range(Number(hourly.time()), Number(hourly.timeEnd()), hourly.interval()).map((t) =>
        new Date((t + utcOffsetSeconds) * 1000).toLocaleString()
      ),
      temperature2m: Array.from(hourly.variables(0)!.valuesArray()!, (x: number) => Math.round(x)),
      precipitation: Array.from(hourly.variables(1)!.valuesArray()!, (x) => x.toFixed(2)),
      surfacePressure: Array.from(hourly.variables(2)!.valuesArray()!, (x) => Math.round(x)),
      cloudCover: Array.from(hourly.variables(3)!.valuesArray()!, (x) => Math.round(x)),
      windSpeed10m: Array.from(hourly.variables(4)!.valuesArray()!, (x) => Math.round(x))
    }
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
    console.log('forecast', data)
    emit('updateForecast', data)
  }
)
</script>

<template>
  <div v-if="town">{{ town }}, {{ timezone }}</div>
</template>

<style scoped></style>
