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
import { useAxios } from "@/hooks/useAxios";

const { vAlert, vSuccess } = useAlert();

const router = useRouter();
const form = ref({
  title: null,
  content: null,
});

const { error, loading, execute } = useAxios(
  "/posts",
  {
    method: "post",
  },
  {
    immediate: false,
    onSuccess: () => {
      router.push({ name: "PostList" });
      vSuccess("등록이 완료되었습니다.");
    },
    onError: (err) => {
      vAlert(err.message);
    },
  }
);

const save = async () => {
  execute({ ...form.value, createdAt: Date.now() });
};

const goListPage = () => {
  router.push({ name: "PostList" });
};
const visibleForm = ref(true);
</script>

<style lang="scss" scoped></style>
