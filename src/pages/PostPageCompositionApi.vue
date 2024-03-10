<template>
  <div>
    <h1>Страница с постами</h1>
    <my-input
        v-model="searchQuery"
        placeholder="Поиск...."
        v-focus
    />
    <div class="app__btns">
      <my-button
      >
        Создать пользователя
      </my-button>
      <my-select
          v-model="selectedSort"
          :options="sortOptions"
      />
    </div>
    <my-dialog v-model:show="dialogVisible">
      <post-form
      />
    </my-dialog>
    <post-list
        :posts="sortedAndSearchedPosts"
        v-if="!isPostsLoading"
    />
    <div v-else>Идет загрузка...</div>
  </div>
</template>




<script>
import PostForm from "@/Components/PostForm.vue";
import PostList from "@/Components/PostList.vue";
import MyButton from "@/Components/UI/MyButton.vue";
import MySelect from "@/Components/UI/MySelect.vue";
import MyInput from "@/Components/UI/MyInput.vue";
import useSortedPosts from "@/hooks/useSortedPosts";
import useSortedAndSearchedPosts from "@/hooks/sortedAndSearchedPosts";
import {usePost} from "@/hooks/usePost";
export default {
  components: {
    MyInput,
    MySelect,
    MyButton,
    PostForm, PostList
  },
  data(){
    return {
      dialogVisible: false,
      sortOption: [
        {value: 'title', name: 'По названию'},
        {value: 'body', name: 'По содержимому'}
      ]
    }
  },

  //
  //
  //пока не знаю почему не работает
  //
  //


  setup(props){
    const {posts, totalPages, isPostLoading} = usePost(10);
    const {sortedPosts, selectedSort} = useSortedPosts(posts);
    const {searchQuery, sortedAndSearchedPosts} = useSortedAndSearchedPosts(sortedPosts);

    return{
      posts,
      totalPages,
      isPostLoading,
      sortedPosts,
      selectedSort,
      searchQuery,
      sortedAndSearchedPosts
    }
  }
}
</script>



<style scoped>

.app__btns{
  margin: 15px 0;
  display: flex;
  justify-content: space-between;
}
</style>