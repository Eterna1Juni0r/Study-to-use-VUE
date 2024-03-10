<template>
  <div>
    <h1>Страница с постами</h1>
    <my-input
        v-focus
        v-model="searchQuery"
        placeholder="Поиск..."
    />
    <div class="app__btns">
      <my-button
          @click="showDialog"
      >
        Создать пост
      </my-button>
      <my-select
          v-model="selectedSort"
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
    <!--    <div class="page__wrapper">-->
    <!--      <div-->
    <!--          v-for="pageNumber in totalPages"-->
    <!--          :key="pageNumber"-->
    <!--          class="page"-->
    <!--          :class="{-->
    <!--            'current_page': page === pageNumber-->
    <!--          }"-->
    <!--          @click="changePage(pageNumber)"-->
    <!--      >-->
    <!--        {{ pageNumber }}-->
    <!--      </div>-->
    <!--    </div>-->
  </div>
</template>




<script>
import PostForm from "@/Components/PostForm.vue";
import PostList from "@/Components/PostList.vue";
import MyDialog from "@/Components/UI/MyDialog.vue";
import MyButton from "@/Components/UI/MyButton.vue";
import axios from 'axios';
import MySelect from "@/Components/UI/MySelect.vue";
import MyInput from "@/Components/UI/MyInput.vue";
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
      posts: [],
      dialogVisible: false,
      isPostLoading: false,
      selectedSort: '',
      searchQuery:'',
      page: 1,
      limit: 10,
      totalPages: 0,
      sortOption: [
        {value: 'title', name: 'По названию'},
        {value: 'body', name: 'По содержимому'}
      ]
    }
  },
  methods:{
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
    //changePage(pageNumber){
    //   this.page = pageNumber
    // },
    async loadMorePosts(){
      try{
        this.page += 1;
        this.isPostLoading = true;
        const response = await axios.get('https://jsonplaceholder.typicode.com/posts',{
          params: {
            _page: this.page,
            _limit: this.limit,
          }
        });
        this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
        this.posts = [...this.posts, ...response.data];

      }catch (e) {
        alert('Ошибка')
      }
    },
    async fetchPost(){
      try{
        this.isPostLoading = true;
        const response = await axios.get('https://jsonplaceholder.typicode.com/posts',{
          params: {
            _page: this.page,
            _limit: this.limit,
          }
        });
        this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
        this.posts = response.data;

      }catch (e) {
        alert('Ошибка')
      }
    }
  },
  mounted() {
    this.fetchPost();
    // const options = {
    //   rootMargin: '0px',
    //   threshold: 1.0
    // }
    // const callback = (entries) => {
    //   if(entries[0].isIntersecting && this.page < this.totalPages){
    //     this.loadMorePosts()
    //   }
    // };
    // const observer = new IntersectionObserver(callback, options);
    // observer.observe(this.$refs.observer);
  },
  computed:{
    sortedPost(){
      return[...this.posts].sort((post1, post2) => {
        return post1[this.selectedSort]?.localeCompare(post2[this.selectedSort])})
    },
    sortedAndSearchedPost(){
      return this.sortedPost.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
    },
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