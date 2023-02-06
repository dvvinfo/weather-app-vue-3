<template>
  <div class="flex flex-col flex-1 items-center">
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-weather-secondary w-full text-center"
    >
      <p>
        В данный момент вы просматриваете этот город, нажмите на значок "+",
        чтобы начать отслеживать этот город
      </p>
    </div>
    <!-- Weather Overlay -->
    <div
      class="flex flex-col items-center text-white my-5 py-12 shadow-lg max-w-screen-md w-full border-custom"
    >
      <h2 class="text-4xl mb-2">{{ route.params.city }}</h2>
      <p class="text-sm mb-12">
        {{
          new Date(weatherData.currentTime).toLocaleDateString("ru-ru", {
            weekday: "short",
            day: "2-digit",
            month: "long",
          })
        }}
        {{
          new Date(weatherData.currentTime).toLocaleTimeString("ru-ru", {
            timeStyle: "short",
          })
        }}
      </p>
      <p class="text-8xl mb-8">
        {{ Math.round(weatherData.current.temp) }}&deg;
      </p>
      <p class="mb-1">
        Ощущаеться
        {{ Math.round(weatherData.current.feels_like) }} &deg;
      </p>
      <p class="capitalize">
        {{ weatherData.current.weather[0].description }}
      </p>

      <img
        class="w-[150px] h-auto"
        :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`"
        alt=""
      />
    </div>

    <!-- Hourly Weather -->
    <div class="max-w-screen-md w-full py-12 my-5 shadow-lg">
      <div class="mx-8 text-white">
        <h2 class="mb-4">Почасовая погода</h2>
        <div class="custom-scroll flex gap-10 overflow-x-scroll pb-5">
          <div
            v-for="hourData in weatherData.hourly"
            :key="hourData.dt"
            class="flex flex-col gap-4 items-center"
          >
            <p class="whitespace-nowrap text-md">
              {{
                new Date(hourData.currentTime).toLocaleTimeString("ru-ru", {
                  hour: "numeric",
                })
              }}
            </p>
            <img
              class="w-auto h-[50px] object-cover"
              :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
              alt=""
            />
            <p class="text-xl">{{ Math.round(hourData.temp) }}&deg;</p>
          </div>
        </div>
      </div>
    </div>

    <!-- Weekly Weather -->
    <div class="max-w-screen-md w-full py-12 my-5 shadow-lg">
      <div class="mx-8 text-white">
        <h2 class="mb-4">Прогноз на 7 дней</h2>
        <div
          v-for="day in weatherData.daily"
          :key="day.dt"
          class="flex items-center"
        >
          <p class="flex-1">
            {{
              new Date(day.dt * 1000).toLocaleDateString("ru-ru", {
                weekday: "long",
              })
            }}
          </p>
          <img
            class="w-[50px] h-[50px] object-cover"
            :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`"
            alt=""
          />
          <div class="flex gap-2 flex-1 justify-end">
            <p>{{ Math.round(day.temp.max) }}</p>
            <p>/</p>
            <p>{{ Math.round(day.temp.min) }}</p>
          </div>
        </div>
      </div>
    </div>

    <div
      @click="removeCity"
      class="flex items-center gap-2 py-8 text-white cursor-pointer duration-150 hover:text-red-500"
    >
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";

const route = useRoute();
const APIKey = "843d1a3ab8b14edafa2cc046a3ecf10e";
const router = useRouter();

const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=${APIKey}&units=metric&lang=ru`
    );

    // cal current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.current.dt * 1000 + localOffset;
    weatherData.data.currentTime =
      utc + 1000 * weatherData.data.timezone_offset;
    // cal hourly weather offset
    weatherData.data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
    });

    // Flicker Delay
    await new Promise((res) => setTimeout(res, 1000));

    return weatherData.data;
  } catch (error) {
    console.log(error);
  }
};

const weatherData = await getWeatherData();

const removeCity = () => {
  const cites = JSON.parse(localStorage.getItem("savedCities"));
  const updatedCities = cites.filter((city) => city.id !== route.query.id);
  localStorage.setItem("savedCities", JSON.stringify(updatedCities));
  router.push({
    name: "home",
  });
};
</script>

<style lang="scss" scoped>
.custom-scroll {
  scrollbar-width: thin;
  scrollbar-color: #004e71 #3eb3dd;
}
.custom-scroll::-webkit-scrollbar {
  width: 12px;
}
.custom-scroll::-webkit-scrollbar-track {
  background: #004e71;
}
.custom-scroll::-webkit-scrollbar-thumb {
  background-color: #3eb3dd;
  border-radius: 20px;
}
</style>
