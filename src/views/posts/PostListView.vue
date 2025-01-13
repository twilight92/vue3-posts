<template>
  <div>
    <h2>게시글 목록</h2>
    <hr class="my-4" />
    <post-filter
      v-model:title="params.title_like"
      v-model:limit="params._limit"
    />

    <hr class="my-4" />

    <app-loading v-if="loading" />

    <app-error v-else-if="error" :message="error.message" />

    <template v-else>
      <app-grid :items="posts">
        <template v-slot="{ item }">
          <post-item
            :title="item.title"
            :content="item.content"
            :created-at="item.createdAt"
            @click="goPage(item.id)"
            @modal="openModal(item)"
          ></post-item>
        </template>
      </app-grid>

      <app-pagination
        :current-page="params._page"
        :page-count="pageCount"
        @page="(page) => (params._page = page)"
      />
    </template>

    <teleport to="#modal">
      <post-modal
        v-model="show"
        :title="modalTitle"
        :content="modalContent"
        :created-at="modalCreatedAt"
      />
    </teleport>
    <hr class="my-4" />

    <hr class="my-5" />
    <app-card>
      <post-detail-view :id="1"></post-detail-view>
    </app-card>
  </div>
</template>

<script setup>
import PostItem from "@/components/posts/PostItem.vue";
import PostDetailView from "@/views/posts/PostDetailView.vue";
import PostFilter from "@/views/posts/PostFilter.vue";
import PostModal from "@/views/posts/PostModal.vue";
import { getPosts } from "@/api/posts";
import { computed, ref, watchEffect } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();
const posts = ref([]);
const error = ref(null);
const loading = ref(false);
const params = ref({
  _sort: "createdAt",
  _order: "desc",
  _page: 1,
  _limit: 3,
  title_like: "",
});
// pagination
const totalCount = ref(0);
const pageCount = computed(() =>
  Math.ceil(totalCount.value / params.value._limit)
);

const fetchPosts = async () => {
  try {
    loading.value = true;
    const { data, headers } = await getPosts(params.value);
    posts.value = data;
    totalCount.value = headers["x-total-count"];
  } catch (err) {
    error.value = err;
  } finally {
    loading.value = false;
  }
};
// fetchPosts();
watchEffect(fetchPosts);
const goPage = (id) => {
  // router.push(`/posts/${id}`);
  router.push({
    name: "PostDetail",
    params: {
      id,
    },
  });
};
// modal
const show = ref(false);
const modalTitle = ref("");
const modalContent = ref("");
const modalCreatedAt = ref("");
const openModal = ({ title, content, createdAt }) => {
  show.value = true;
  modalTitle.value = title;
  modalContent.value = content;
  modalCreatedAt.value = createdAt;
};
</script>

<style lang="scss" scoped></style>
