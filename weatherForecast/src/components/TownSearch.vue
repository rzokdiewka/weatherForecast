<script setup lang="ts">
import { onMounted, ref, watch } from 'vue'

const townInput = ref()
let queryParams = null
const townCoordinatesUrl = ref()
const lon = ref()
const lat = ref()
const name = ref(null)
const refresh = ref<boolean>(false)

const emit = defineEmits(['updateCoordinates', 'refresh'])
let input: HTMLDataElement = document.getElementById("search-input")

onMounted(()=> {
  input = document.getElementById("search-input")

  input.addEventListener("keypress", function(event) {
    if (event.key === "Enter") {
      event.preventDefault();
      document.getElementById("search-button").click()
    }
  });
})

const getTownCoordinates = async () => {
  await fetch(townCoordinatesUrl.value)
    .then((response) => response.json())
    .then((data) => {
      console.log('data', data)
      lon.value = data['results'][0].longitude
      lat.value = data['results'][0].latitude
      name.value = data['results'][0].name
    })
  emit('updateCoordinates', { name, lon, lat })
  refresh.value = !refresh.value
  emit('refresh', refresh.value)
}
watch(
  () => townInput.value,
  () => {
    queryParams = new URLSearchParams({ name: townInput.value })
    townCoordinatesUrl.value = `https://geocoding-api.open-meteo.com/v1/search?${queryParams}&count=1&language=en&format=json`
  }
)
</script>

<template>
  <div class="search">
    <input id="search-input" v-model="townInput" type="text" placeholder="Insert town" />
    <button id="search-button"  @click="getTownCoordinates">Fetch forecast</button>
  </div>
</template>

<style scoped>
input[type='text'] {
  background-image: url('@/../public/search.svg');
  background-position: 0.5rem 0.5rem;
  background-repeat: no-repeat;
  padding: 0.75rem 1rem 0.75rem 2.5rem;
}

.search {
  display: flex;
  flex-direction: row;
}
</style>
