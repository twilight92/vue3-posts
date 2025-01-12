<template>
  <nav class="my-5" aria-label="...">
    <ul class="pagination justify-content-center">
      <li class="page-item" :class="isPrevPage">
        <span class="page-link" @click.prevent="$emit('page', currentPage - 1)"
          >Previous</span
        >
      </li>
      <li
        v-for="page in pageCount"
        :key="page"
        class="page-item"
        :class="{ active: currentPage === page }"
      >
        <a class="page-link" href="#" @click.prevent="$emit('page', page)">{{
          page
        }}</a>
      </li>
      <li class="page-item" :class="isNextPage">
        <a
          class="page-link"
          href="#"
          @click.prevent="$emit('page', currentPage + 1)"
          >Next</a
        >
      </li>
    </ul>
  </nav>
</template>

<script setup>
import { computed } from "vue";
const props = defineProps({
  currentPage: {
    type: Number,
    required: true,
  },
  pageCount: {
    type: Number,
    required: true,
  },
});

defineEmits(["page"]);

console.log(props.currentPage);
console.log(props.pageCount);

const isPrevPage = computed(() => ({ disabled: !(props.currentPage > 1) }));
const isNextPage = computed(() => ({
  disabled: !(props.currentPage < props.pageCount),
}));
</script>
