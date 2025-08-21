<template>
  <div v-for="city in savedCities" :key="city.id">
    <CityCard :city="city" @click="goToCityView(city)" />
  </div>

  <p v-if="savedCities.length === 0">
    No location added. To start tracking a location, search in the field above.
  </p>
</template>

<script setup>
import axios from 'axios'
import { ref } from 'vue'
import CityCard from './CityCard.vue'
import { useRouter } from 'vue-router'
const weatherApiKEY = '4c23348466aa48a9b5e212852251105'
const savedCities = ref([])
const getCities = async () => {
  if (localStorage.getItem('savedCities')) {
    savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
  }
  const request = []
  savedCities.value.forEach((city) => {
    request.push(
      axios.get(
        `https://api.weatherapi.com/v1/current.json?key=${weatherApiKEY}&q=${city.coords.lat},${city.coords.lon}`,
      ),
    )
  })
  const weatherData = await Promise.all(request)
  await new Promise((resolve) => setTimeout(resolve, 1000))
  weatherData.forEach((value, index) => {
    savedCities.value[index].weather = value.data
  })
}

await getCities()

const router = useRouter()
const goToCityView = (city) => {
  router.push({
    name: 'cityView',
    params: { state: city.state, city: city.city },
    query: { id: city.id, lat: city.coords.lat, lon: city.coords.lon },
  })
}
</script>
