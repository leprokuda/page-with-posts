<template>
  <div>
    <h1>Страница с постами</h1>
    <my-input
      :model-value="searchQuery"
      @update:model-value="setSearchQuery"
      placeholder="Поиск..."
      v-focus
    />
    <div class="app__btns">
      <my-button
        @click="showDialog"
      >
        Создать пост
      </my-button>
      <my-select
        :model-value="selectedSort"
        @update:model-value="setSelectedSort"
        :options="sortOptions"
      />
    </div>
    <my-dialog v-model:show="dialogVisible">
      <PostForm 
      @create="createPost"
      />
    </my-dialog>
    <PostList
      :posts="sortedAndSearchedPosts"
      @remove="removePost"
      style="margin-top: 30px"
    />
    <is-preloader/>
    <div class="page__wrapper">
      <div
        v-for="pageNumber in totalPages"
        :key="pageNumber"
        class="page"
        :class="{
          'current-page': page === pageNumber
        }"
        @click="changePage(pageNumber)"
      >
        {{pageNumber}}
      </div>
    </div>
  </div>
</template>

<script>
import PostForm from "@/components/PostForm.vue";
import PostList from "@/components/PostList.vue";
import MyDialog from "@/components/UI/MyDialog.vue";
import axios from 'axios'
import IsPreloader from '@/components/UI/IsPreloader.vue'
import MySelect from "@/components/UI/MySelect.vue";
import MyInput from "@/components/UI/MyInput.vue";
import MyButton from "@/components/UI/MyButton.vue";
import {mapState, mapGetters, mapMutations, mapActions} from 'vuex'


export default {

  data() {
    return {
      dialogVisible: false,
    }
  },
  methods: {
    ...mapMutations({
      setPage: 'post/setPage',
      setSearchQuery: 'post/setSearchQuery',
      selectedSort: 'post/setSelectedSort'
    }),
    ...mapActions({
      loadMorePosts: 'post/loadMorePosts',
      fetchPosts: 'post/fetchPosts'
    }),
    createPost(post) {
      this.posts.push(post)
      this.dialogVisible = false
    },
    removePost(post) {
      this.posts =this.posts.filter(p => p.id !== post.id)
    },
    showDialog() {
      this.dialogVisible = true
    },
  },
  mounted() {
    this.fetchPosts()
  },
  computed: {
    ...mapState({
      posts: state => state.post.posts,
      selectedSort: state => state.post.selectedSort,
      searchQuery: state => state.post.searchQuery,
      page: state => state.post.page,
      limit: state => state.post.limit,
      totalPages: state => state.post.totalPages,
      sortOptions: state => state.post.sortOptions,
    }),
    ...mapGetters({
      sortedPost: 'post/sortedPosts',
      sortedAndSearchedPosts: 'post/sortedAndSearchedPosts',
    })
  },
  watch: {
    page() {
      this.fetchPosts()
    }
  },
  components: { PostForm, PostList, MyDialog, IsPreloader, MySelect, MyInput, MyButton }
}
</script>

<style>
  .app__btns {
    display: flex;
    justify-content: space-between;
    margin-top: 15px
  }

  .page__wrapper {
    display: flex;
    justify-content: center;
    margin-top: 15px;
  }

  .page {
    border: 1px solid black;
    display: flex;
    width: 25px;
    height: 25px;
    justify-content: center;
    align-items: center;
    cursor: pointer;
  }

  .current-page {
    border: 2px solid teal;
  }
</style>