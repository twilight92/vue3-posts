<template>
  <app-loading v-if="loading" />

  <app-error v-else-if="error" :message="error.message" />

  <div v-else>
    <h2>{{ post.title }}</h2>
    <p>{{ post.content }}</p>
    <p class="text-muted">
      {{ $dayjs(post.createdAt).format("YYYY.MM.DD HH:mm:ss") }}
    </p>
    <hr class="my-4" />
    <app-error v-if="removeError" :message="removeError.message" />
    <div class="row g-2">
      <div class="col-auto">
        <button class="btn btn-outline-dark">이전글</button>
      </div>
      <div class="col-auto">
        <button class="btn btn-outline-dark">다음글</button>
      </div>
      <div class="col-auto me-auto"></div>
      <div class="col-auto">
        <button class="btn btn-outline-dark" @click="goListPage">목록</button>
      </div>
      <div class="col-auto">
        <button class="btn btn-outline-primary" @click="goEditPage">
          수정
        </button>
      </div>
      <div class="col-auto">
        <button
          class="btn btn-outline-danger"
          :disabled="removeLoading"
          @click="remove"
        >
          <template v-if="removeLoading">
            <span
              class="spinner-grow spinner-grow-sm"
              aria-hidden="true"
            ></span>
            <span class="visually-hidden" role="status">Loading...</span>
          </template>
          <template v-else> 삭제 </template>
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { useRouter } from "vue-router";
import { useAlert } from "@/composables/alert";
import { useAxios } from "@/hooks/useAxios";
import { computed } from "vue";

const props = defineProps({
  id: String,
});

const { vAlert, vSuccess } = useAlert();
const url = computed(() => `/posts/${props.id}`);

const router = useRouter();

const { error, loading, data: post } = useAxios(url);

const {
  error: removeError,
  loading: removeLoading,
  execute,
} = useAxios(
  `/posts/${props.id}`,
  { method: "delete" },
  {
    immediate: false,
    onSuccess: () => {
      vSuccess("삭제가 완료되었습니다.");
      router.push({ name: "PostList" });
    },
    onError: (err) => {
      vAlert(err.message);
    },
  }
);

const remove = async () => {
  if (confirm("삭제할거임?") === false) {
    return;
  }
  execute();
};
const goListPage = () => {
  router.push({
    name: "PostList",
  });
};
const goEditPage = () => {
  router.push({
    name: "PostEdit",
    params: { id: props.id },
  });
};
</script>

<style lang="scss" scoped></style>
