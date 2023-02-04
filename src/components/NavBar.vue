<template>
  <header class="sticky top-0 bg-weather-primary shadow-lg">
    <nav
      class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6"
    >
      <router-link :to="{ name: 'home' }">
        <div class="flex items-center gap-3">
          <i class="fa-solid fa-sun text-2xl"></i>
          <p class="text-2xl">Местная погода</p>
        </div>
      </router-link>
      <div class="flex gap-3 flex-1 justify-end">
        <i
          class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
          @click="toggleModal"
        ></i>
        <i
          @click="addCity"
          v-if="route.query.preview"
          class="fa-solid fa-plus text-xl hover:text-weather-secondary duration-150 cursor-pointer"
        ></i>
      </div>

      <!-- modal -->

      <base-modal :modalActive="modalActive" @close-modal="toggleModal">
        <div class="text-black">
          <h1 class="text-2xl mb-1">О сервисе</h1>
          <p class="mb-4">
            Местная погода позволяет вам отслеживать текущую и будущую погоду из
            городов по вашему выбору.
          </p>
          <h2 class="text-2xl">Как это работает:</h2>
          <ol class="list-decimal list-inside mb-4">
            <li>Найдите свой город, введя название в строку поиска..</li>
            <li>
              Выберите город в результатах, это приведет вас к текущей погоде
              для вашего выбора.
            </li>
            <li>
              Отслеживайте город, нажав на значок "+" в правом верхнем углу.
              Этот сохранит город для последующего просмотра на домашней
              странице.
            </li>
          </ol>

          <h2 class="text-2xl">Удаление города</h2>
          <p>
            Если вы больше не хотите отслеживать город, просто выберите его на
            домашней странице. В нижней части страницы будет возможность удалить
            город.
          </p>
        </div>
      </base-modal>
    </nav>
  </header>
</template>

<script setup>
import { ref } from "@vue/reactivity";
import { uid } from "uid";
import { RouterLink, useRoute, useRouter } from "vue-router";
import BaseModal from "./BaseModal.vue";

const modalActive = ref(null);
const savedCites = ref([]);
const route = useRoute();
const router = useRouter();

const toggleModal = () => {
  modalActive.value = !modalActive.value;
};

const addCity = () => {
  if (localStorage.getItem("saveCities")) {
    savedCites.value = JSON.parse(localStorage.getItem("saveCities"));
  }
  const locationObj = {
    id: uid(),
    state: route.params.state,
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lng: route.query.lng,
    },
  };
  savedCites.value.push(locationObj);
  localStorage.setItem("savedCities", JSON.stringify(savedCites.value));

  let query = Object.assign({}, route.query);
  delete query.preview;
  query.id = locationObj.id;
  router.replace({ query });
};
</script>

<style lang="scss" scoped></style>
