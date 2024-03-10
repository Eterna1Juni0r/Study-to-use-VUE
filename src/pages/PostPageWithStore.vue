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
          :options="sortOption"
      />
    </div>
    <my-dialog v-model:show="dialogVisible">
      <post-form
          @create="createPost"
      />
    </my-dialog>
    <post-list
        :posts="sortedAndSearchedPost"
        @remove="removePost"
    />
    <div v-intersection="loadMorePosts" class="observer"></div>
        <div class="page__wrapper">
          <div
              v-for="pageNumber in totalPages"
              :key="pageNumber"
              class="page"
              :class="{
                'current_page': page === pageNumber
              }"
              @click="changePage(pageNumber)"
          >
            {{ pageNumber }}
          </div>
        </div>
  </div>
</template>




<script>
import PostForm from "@/Components/PostForm.vue";
import PostList from "@/Components/PostList.vue";
import MyDialog from "@/Components/UI/MyDialog.vue";
import MyButton from "@/Components/UI/MyButton.vue";
import MySelect from "@/Components/UI/MySelect.vue";
import MyInput from "@/Components/UI/MyInput.vue";
import {mapState, mapMutations, mapActions, mapGetters} from "vuex";
export default {
  components: {
    MyInput,
    MySelect,
    MyButton,
    MyDialog,
    PostForm, PostList
  },
  data(){
    return {
      dialogVisible: false,
    }
  },
  methods:{
    ...mapMutations({
      setPageL: 'post/setPage',
      setSearchQuery: 'post/setSearchQuery',
      setSelectedSort: 'post/setSelectedSort'
    }),
    ...mapActions({
      loadMorePosts:'post/loadMorePosts',
      fetchPost:'post/fetchPost'
    }),
    createPost(post){
      this.posts.push(post);
      this.dialogVisible = false;
    },
    removePost(post){
      this.posts = this.posts.filter(p => p.id !== post.id)
    },
    showDialog(){
      this.dialogVisible = true;
    },
  },
  mounted() {
    this.fetchPost()
  },
  computed:{
    ...mapState({
      posts: state => state.post.posts,
      isPostLoading: state => state.post.isPostLoading,
      selectedSort :state => state.post.selectedSort,
      searchQuery: state => state.post.searchQuery,
      page: state => state.post.page,
      limit: state => state.post.limit,
      totalPages: state => state.post.totalPages,
      sortOption: state => state.post.sortOption
    }),
    ...mapGetters({
      sortedPost: 'post/sortedPost',
      sortedAndSearchedPost: 'post/sortedAndSearchedPost'
    })
  },
  watch: {
    // page(){
    //   this.fetchPost()
    // }
  }
}
</script>



<style scoped>

.app__btns{
  margin: 15px 0;
  display: flex;
  justify-content: space-between;
}
.page__wrapper{
  display: flex;
  margin-top: 15px;
}
.page{
  border: 1px solid black;
  padding: 10px ;
}
.current_page{
  border: 3px solid teal;
}

.observer{
  height: 30px;
  background: green;

}
</style>