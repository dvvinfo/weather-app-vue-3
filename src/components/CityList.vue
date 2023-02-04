<template>
  <div v-for="city in savedCities" :key="city.id">
    <city-card :city="city" @click="goToCityView(city)" />
  </div>
  <p v-if="savedCities.length === 0">
    Местоположения не добавлены. Чтобы начать отслеживать местоположение,
    выполните поиск в поле выше.
  </p>
</template>

<script setup>
import { ref } from "@vue/reactivity";
import axios from "axios";
import { useRouter } from "vue-router";
import CityCard from "./CityCard.vue";

const savedCities = ref([]);
const APIKey = "843d1a3ab8b14edafa2cc046a3ecf10e";
const router = useRouter();

const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
    const requests = [];
    savedCities.value.forEach((city) => {
      requests.push(
        axios.get(
          `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=${APIKey}&units=metric&lang=ru`
        )
      );
    });
    const weatherData = await Promise.all(requests);
    
    // Flicker Delay
    await new Promise((res) => setTimeout(res, 1000))

    weatherData.forEach((value, index) => {
      savedCities.value[index].weather = value.data;
    });
  }
};
await getCities();
const goToCityView = (city) => {
  router.push({
    name: "cityView",
    params: { state: city.state, city: city.city },
    query: {
      id: city.id,
      lat: city.coords.lat,
      lng: city.coords.lng,
    },
  });
};
</script>

<style lang="scss" scoped></style>
