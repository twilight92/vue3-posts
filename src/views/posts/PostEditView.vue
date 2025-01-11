<template>
  <div>
    <h2>게시글 수정</h2>
    <hr class="my-4" />
    <post-form
      v-model:title="form.title"
      v-model:content="form.content"
      @submit.prevent="edit"
    >
      <template #actions>
        <button
          type="button"
          class="btn btn-outline-danger"
          @click="goDetailPage"
        >
          취소
        </button>
        <button class="btn btn-primary">수정</button>
      </template>
    </post-form>
    <!-- <app-alert :show="showAlert" :message="alertMessage" :type="alertType" /> -->
    <app-alert :items="alerts" />
  </div>
</template>

<script setup>
import { ref } from "vue";
import { useRoute, useRouter } from "vue-router";
import { getPostById, updatePost } from "@/api/posts";
import PostForm from "@/components/posts/PostForm.vue";
import AppAlert from "@/components/AppAlert.vue";

const route = useRoute();
const router = useRouter();
const id = route.params.id;

const form = ref({
  title: null,
  content: null,
});
const fetchPost = async () => {
  try {
    const { data } = await getPostById(id);
    setForm(data);
  } catch (error) {
    console.error(error);
    vAlert(error.message);
  }
};
const setForm = ({ title, content, createdAt }) => {
  form.value.title = title;
  form.value.content = content;
  form.value.createdAt = createdAt;
};
fetchPost();

const goDetailPage = () => {
  router.push({
    name: "PostDetail",
    params: { id },
  });
};
const edit = async () => {
  try {
    await updatePost(id, { ...form.value });
    // router.push({ name: "PostDetail", params: { id } });
    vAlert("수정이 완료되었습니다", "success");
  } catch (error) {
    console.error(error);
    vAlert(error.message);
  }
};

// alert
// const showAlert = ref(false);
// const alertMessage = ref("false");
// const alertType = ref("");
const alerts = ref([]);
const vAlert = (message, type = "error") => {
  alerts.value.push({ message, type });
  setTimeout(() => {
    // showAlert.value = false;
    alerts.value.shift();
  }, 2000);
};
</script>

<style lang="scss" scoped></style>
