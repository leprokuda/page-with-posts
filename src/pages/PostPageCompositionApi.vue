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
import {ref} from 'vue';
import useSortedPosts from "@/hooks/useSortedPosts";
import useSortedAndSearchedPosts from '@/hooks/useSortedAndSearchedPosts'


export default {

  data() {
    return {
      dialogVisible: false,
      sortOptions: [
        {value: 'title', name: 'По названию'},
        {value: 'body', name: 'По содержимому'},
      ]
    }
  },
  setup(props) {
    const {posts, totalPages} = usePosts(10)
    const {sortedPosts, selectedSort} = useSortedPosts(posts)
    const {searchQuery, useSortedAndSearchedPosts} = useSortedAndSearchedPosts(sortedPosts)
    return {
      posts, totalPages, sortedPosts, selectedSort, searchQuery, useSortedAndSearchedPosts
    }
  },
  components: { PostForm, PostList, MyDialog, IsPreloader, MySelect, MyInput, MyButton, ref }
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