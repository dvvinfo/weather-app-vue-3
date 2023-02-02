<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
    <nav-bar />
    <router-view />
  </div>
</template>

<script setup>
import { RouterView } from "vue-router";
import NavBar from "./components/NavBar.vue";
</script>

<style lang="scss" scoped></style>
