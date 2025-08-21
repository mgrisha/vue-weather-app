<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004e71]"
        type="text"
        placeholder="Search for a city or state"
        v-model="searchQuery"
        @input="getSearchResult"
      />
      <ul
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="geoapifySearchResults"
      >
        <li v-if="searchError">Sorry, something went wrong, please try again.</li>
        <li v-if="!searchError && geoapifySearchResults.length === 0">
          No results match your query, try differnt term.
        </li>
        <li
          v-for="searchResult in geoapifySearchResults"
          :key="searchResult.id"
          class="py-2 cursor-pointer"
          @click="previewCity(searchResult)"
        >
          {{ searchResult.formatted }}
        </li>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <CityCardSkeleton />
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'
import CityList from '../components/CityList.vue'
import CityCardSkeleton from '@/components/CityCardSkeleton.vue'

const router = useRouter()

const geoapifyAPIKey = 'af79ece517fd4be6a90558dae997cb40'
// const openWeathermapAPIKey = '660ce7f4d0e336dfe051d1307c9a1a8e'
const searchQuery = ref('')
const queryTimeout = ref(null)
const geoapifySearchResults = ref(null)
const searchError = ref(null)

const getSearchResult = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== '') {
      try {
        const result = await axios.get(
          `https://api.geoapify.com/v1/geocode/search?text=${searchQuery.value}&type=locality&format=json&apiKey=${geoapifyAPIKey}`,
        )
        geoapifySearchResults.value = result.data.results
        console.log(geoapifySearchResults.value)
      } catch {
        searchError.value = true
      }
      return
    }
    geoapifySearchResults.value = null
  }, 500)
}
const previewCity = (searchResult) => {
  const resultType = searchResult.result_type
  const city = searchResult[resultType]
  const state = searchResult.state
  router.push({
    name: 'cityView',
    params: { state: state, city: city },
    query: {
      lat: searchResult.lat,
      lon: searchResult.lon,
      preview: true,
    },
  })
}
</script>
