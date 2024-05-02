<script setup lang="ts">
import { fetchWeatherApi } from 'openmeteo'
import { WeatherApiResponse } from '@openmeteo/sdk/weather-api-response'

import { ref, watch } from 'vue'

const emit = defineEmits(['updateForecast'])

const props = defineProps({
  lon: Number,
  lat: Number
})

export interface WeatherData {
  current: {
    time: Date
    temperature2m: Number
    isDay: Number
    precipitation: Number
    cloudCover: Number
    surfacePressure: Number
    windSpeed10m: Number
    windDirection10m: Number
  }
  hourly: {
    time: Date[]
    temperature2m: Float32Array
    precipitation: Float32Array
    surfacePressure: Float32Array
    cloudCover: Float32Array
    windSpeed10m: Float32Array
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
const data = async () => {
  const responses = await fetchWeatherApi(url, params)

  // Process first location. Add a for-loop for multiple locations or weather models
  forecastData.value = responses[0]
}

// console.log(response)
const transformData = (response: WeatherApiResponse) => {
  // Helper function to form time ranges
  const range = (start: number, stop: number, step: number) =>
    Array.from({ length: (stop - start) / step }, (_, i) => start + i * step)

  // Attributes for timezone and location
  const utcOffsetSeconds = response.utcOffsetSeconds()
  const timezone = response.timezone()
  const timezoneAbbreviation = response.timezoneAbbreviation()
  const latitude = response.latitude()
  const longitude = response.longitude()

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
      temperature2m: hourly.variables(0)!.valuesArray()!,
      precipitation: hourly.variables(1)!.valuesArray()!,
      surfacePressure: hourly.variables(2)!.valuesArray()!,
      cloudCover: hourly.variables(3)!.valuesArray()!,
      windSpeed10m: hourly.variables(4)!.valuesArray()!
    }
  }
}

watch(
  () => props.lon,
  () => {
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
  <div>{{ forecastData }}</div>
</template>

<style scoped></style>
