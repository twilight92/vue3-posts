<template>
  <div>
    <h2>게시글 목록</h2>
    <hr class="my-4" />
    <post-filter
      v-model:title="params.title_like"
      v-model:limit="params._limit"
      @update:limit="changeLimit"
    />

    <hr class="my-4" />

    <app-loading v-if="loading" />

    <app-error v-else-if="error" :message="error.message" />

    <template v-else-if="!isExist">
      <p class="text-center py-5 text-muted">결과 없음</p>
    </template>

    <template v-else>
      <app-grid :items="posts">
        <template v-slot="{ item }">
          <post-item
            :title="item.title"
            :content="item.content"
            :created-at="item.createdAt"
            @click="goPage(item.id)"
            @modal="openModal(item)"
            @preview="selectPreview(item.id)"
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

    <template v-if="previewId">
      <hr class="my-5" />
      <app-card>
        <post-detail-view :id="previewId"></post-detail-view>
      </app-card>
    </template>
  </div>
</template>

<script setup>
import PostItem from "@/components/posts/PostItem.vue";
import PostDetailView from "@/views/posts/PostDetailView.vue";
import PostFilter from "@/views/posts/PostFilter.vue";
import PostModal from "@/views/posts/PostModal.vue";
import { computed, ref } from "vue";
import { useRouter } from "vue-router";
import { useAxios } from "@/hooks/useAxios";

const router = useRouter();

const previewId = ref(null);
const selectPreview = (id) => (previewId.value = id);

const params = ref({
  _sort: "createdAt",
  _order: "desc",
  _page: 1,
  _limit: 3,
  title_like: "",
});

const changeLimit = (value) => {
  params.value._limit = value;
  params.value._page = 1;
};

const {
  response,
  data: posts,
  error,
  loading,
} = useAxios("/posts", { method: "get", params });
const isExist = computed(() => posts.value && posts.value.length > 0);
// pagination
const totalCount = computed(() => response.value.headers["x-total-count"]);
const pageCount = computed(() =>
  Math.ceil(totalCount.value / params.value._limit)
);

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
