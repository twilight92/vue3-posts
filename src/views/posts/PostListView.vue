<template>
  <div>
    <h2>게시글 목록</h2>
    <hr class="my-4" />
    <post-filter
      v-model:title="params.title_like"
      v-model:limit="params._limit"
    />
    <hr class="my-4" />
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
    <app-modal :show="show" title="게시글" @close="closeModal">
      <template #default>
        <div class="row g-3">
          <div class="col-3 text-muted">제목</div>
          <div class="col-9">{{ modalTitle }}</div>
          <div class="col-3 text-muted">내용</div>
          <div class="col-9">{{ modalContent }}</div>

          <div class="col-3 text-muted">등록일</div>
          <div class="col-9">{{ modalCreatedAt }}</div>
        </div>
      </template>
      <template #actions>
        <button type="button" class="btn btn-secondary" @click="closeModal">
          닫기
        </button>
      </template>
    </app-modal>
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
import AppModal from "@/components/AppModal.vue";
import AppPagination from "@/components/AppPagination.vue";
import AppCard from "@/components/AppCard.vue";
import AppGrid from "@/components/AppGrid.vue";
import { getPosts } from "@/api/posts";
import { computed, ref, watchEffect } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();
const posts = ref([]);
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
    const { data, headers } = await getPosts(params.value);
    posts.value = data;
    totalCount.value = headers["x-total-count"];
  } catch (error) {
    console.error(error);
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
const closeModal = () => {
  show.value = false;
};
</script>

<style lang="scss" scoped></style>
