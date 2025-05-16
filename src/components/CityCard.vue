<template>
  <div class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer">
    <div class="flex flex-col flex-1">
      <h2 class="text-3xl">{{ city.city }}</h2>
      <h3>{{ city.state }}</h3>
    </div>

    <div class="flex items-center gap-6">
      <div class="flex">
        <img
          class="w-auto h-[50px] object-cover"
          :src="city.weather.current.condition.icon"
          alt=""
        />
      </div>
      <p class="text-3xl">{{ Math.round(city.weather.current.temp_c) }}&deg;</p>
    </div>
  </div>
</template>

<script setup>
defineProps({
  city: {
    type: Object,
    default: () => {},
  },
})
</script>
