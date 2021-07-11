<template>
  <v-app>
    <Header
    @delete-local-storage="deleteLocalStorage"
    />
      <v-main>
        <v-container>
          <router-view
          @add-book-list='addBook'
          @update-book-info="updateBookInfo"
          :books="books"
          />
        </v-container>
      </v-main>
    <Footer/>
  </v-app>
</template>

<script>
import Header from '@/global/Header';
import Footer from '@/global/Footer';

const STORAGE_KEY = 'books';

export default {
  name: 'App',
  components: {
    Header,
    Footer
  },
  data(){
    return{
      books: [],
      newBook: null
    }
  },
  mounted() {
      if (localStorage.getItem(STORAGE_KEY)) {
      try {
          this.books = JSON.parse(localStorage.getItem(STORAGE_KEY));
      } catch(e) {
          localStorage.removeItem(STORAGE_KEY);
      }
      }
  },
  methods: {
      addBook(e) {
      // 実際に何かしたことを入力する
      // if (!this.newBook) {
      //     return;
      // }
      this.books.push({
        id:this.books.length,
        title:e.title,
        image:e.image,
        description:e.description,
        readDate:'',
        memo:''
      });
      // this.newBook = '';
      this.saveBooks();
      // console.log(this.books.slice(-1)[0].id);
      this.goEditPage(this.books.slice(-1)[0].id)
      },
      removeBook(x) {
      this.books.splice(x, 1);
      this.saveBooks();
      },
      saveBooks() {
      const parsed = JSON.stringify(this.books);
      localStorage.setItem(STORAGE_KEY, parsed);
      },
      updateBookInfo(e){
        const update = {
          id: e.id,
          readDate: e.readDate,
          memo: e.memo,
          title: this.books[e.id].title,
          image: this.books[e.id].image,
          description: this.books[e.id].description
        }

        this.books.splice(e.id,1,update)
        this.saveBooks()
        this.$router.push('/')
      },
      goEditPage(id){
        this.$router.push(`/edit/${id}`)
      },
      deleteLocalStorage(){
        const isDeleted = "LocalStorageのデータを削除しても良いですか？"
        if(window.confirm(isDeleted)) {
          localStorage.setItem(STORAGE_KEY,'');
          localStorage.removeItem(STORAGE_KEY)
          this.books = []
          window.location.reload()
        }
      }
  }
};
</script>
