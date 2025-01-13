<script setup>
import TheHeader from "@/layouts/TheHeader.vue";
import TheView from "@/layouts/TheView.vue";
import { useAlert } from "./composables/alert";
const { alerts } = useAlert();
</script>

<template>
  <the-header></the-header>
  <the-view></the-view>
  <app-alert :items="alerts" />
</template>

<style></style>
