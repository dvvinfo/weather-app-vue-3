<template>
  <div>
    <Suspense>
      <template #default>
        <async-city />
      </template>
      <template #fallback>
        <city-skeleton />
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import AsyncCity from "../components/AsyncCity.vue";
import CitySkeleton from "../components/CitySkeleton.vue";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";
</script>

<style lang="scss" scoped></style>
