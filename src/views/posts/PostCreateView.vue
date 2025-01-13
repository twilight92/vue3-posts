<template>
  <div>
    <h2 @click="visibleForm = !visibleForm">게시글 등록</h2>
    <hr class="my-4" />
    <app-error v-if="error" :message="error.message" />
    <post-form
      v-if="visibleForm"
      v-model:title="form.title"
      v-model:content="form.content"
      @submit.prevent="save"
    >
      <template #actions>
        <button type="button" class="btn btn-outline-dark" @click="goListPage">
          목록
        </button>
        <button class="btn btn-primary" :disabled="loading">
          <template v-if="loading">
            <span
              class="spinner-grow spinner-grow-sm"
              aria-hidden="true"
            ></span>
            <span class="visually-hidden" role="status">Loading...</span>
          </template>
          <template v-else> 저장 </template>
        </button>
      </template>
    </post-form>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import { createPost } from "@/api/posts";
import PostForm from "@/components/posts/PostForm.vue";
import { useAlert } from "@/composables/alert";

const { vAlert, vSuccess } = useAlert();

const router = useRouter();
const form = ref({
  title: null,
  content: null,
});
const loading = ref(false);
const error = ref(null);
const save = async () => {
  try {
    loading.value = true;
    await createPost({
      ...form.value,
      createdAt: Date.now(),
    });
    router.push({ name: "PostList" });
    vSuccess("등록이 완료되었습니다.");
  } catch (err) {
    error.value = err;
    vAlert(err.message);
  } finally {
    loading.value = false;
  }
};
const goListPage = () => {
  router.push({ name: "PostList" });
};
const visibleForm = ref(true);
</script>

<style lang="scss" scoped></style>
