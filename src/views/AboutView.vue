<template>
  <div>
    <h2>About View</h2>
    <p>{{ $route }}</p>
    <button class="btn btn-primary" @click="$router.push('/')">
      Home으로 이동
    </button>
  </div>
  <h2>Store</h2>
  <p>counter2: {{ counter }}</p>
  <p>doubleCount: {{ doubleCount }}</p>
  <p>doubleCountPlusOne: {{ doubleCountPlusOne }}</p>
  <button @click="increment()">Click!!</button>
</template>

<script setup>
import { useRoute } from "vue-router";
import { useCounterStore } from "@/stores/counter";
import { storeToRefs } from "pinia";

const route = useRoute();
console.log("route.path: ", route.path);

const store = useCounterStore();
// state나 getter를 구조분해 할당을 통해 가져오려면 storeToRefs 함수를 사용하여 반응성 유지
const { counter, doubleCount, doubleCountPlusOne } = storeToRefs(store);
// store로 정의된 actions는 바로 사용 가능
const { increment } = store;
</script>

<style lang="scss" scoped></style>
