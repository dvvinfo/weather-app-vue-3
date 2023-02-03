<template>
  <div>
    <Suspense>
      <async-city />
      <template #fallback>
        <p class="text-white">Loading ...</p>
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import AsyncCity from "../components/AsyncCity.vue";
</script>

<style lang="scss" scoped></style>
