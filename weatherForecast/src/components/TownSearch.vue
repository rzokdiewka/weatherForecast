<script setup lang="ts">
import { ref, watch } from 'vue'

const town = ref()
let queryParams = null
const townCoordinatesUrl = ref()
const lon = ref()
const lat = ref()
const emit = defineEmits(['updateCoordinates'])

const data = async () => {
  const response = await fetch(townCoordinatesUrl.value)
    .then((response) => response.json())
    .then((data) => {
      console.log('data', data)
      lon.value = data['results'][0].longitude
      lat.value = data['results'][0].latitude
      emit('updateCoordinates', { lon, lat })
    })
  console.log('resp', response)
}
watch(
  () => town.value,
  () => {
    queryParams = new URLSearchParams({ name: town.value })
    townCoordinatesUrl.value = `https://geocoding-api.open-meteo.com/v1/search?${queryParams}&count=1&language=en&format=json`

    console.log(queryParams)
  }
)
</script>

<template>
  <input v-model="town" />

  <button @click="data">fetch coordinates</button>
</template>

<style scoped></style>
