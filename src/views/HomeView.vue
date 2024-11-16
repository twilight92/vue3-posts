<template>
  <div>
    <h2>Home View</h2>
    <p>{{ $route }}</p>
  </div>
</template>

<script setup>
import { useRoute } from "vue-router";

const route = useRoute();
console.log("route.path: ", route.path);
</script>

<style lang="scss" scoped></style>
