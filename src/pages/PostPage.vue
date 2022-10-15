<template>
  <div>
    <h1>Страница с постами</h1>
    <my-input
      v-model="searchQuery"
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
        v-model="selectedSort"
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


export default {

  data() {
    return {
      posts: [],
      dialogVisible: false,
      selectedSort: '',
      searchQuery: '',
      page: 1,
      limit: 10,
      totalPages: 0,
      sortOptions: [
        {value: 'title', name: 'По названию'},
        {value: 'body', name: 'По содержимому'},
      ]
    }
  },
  methods: {
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
    changePage(pageNumber) {
      this.page = pageNumber
    },
    async fetchPosts() {
      try {
        setTimeout(async () => {
          const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
            params: {
              _page: this.page,
              _limit: this.limit
            }
          })
          this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
          this.posts = response.data
        }, 1000)
      } catch (e) {
        alert('Ошибка')
      } finally {

      }
    },
  },
  mounted() {
    this.fetchPosts()
  },
  computed: {
    sortedPosts() {
      return [...this.posts].sort((post1, post2) => post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]))
    },
    sortedAndSearchedPosts() {
      return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
    }
  },
  watch: {
    page() {
      this.fetchPosts()
    }
  },
  components: { PostForm, PostList, MyDialog, IsPreloader, MySelect, MyButton, MyInput }
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