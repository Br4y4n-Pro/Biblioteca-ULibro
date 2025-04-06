<template>
  <div>
    <!-- Input de búsqueda -->
    <div class="search-box">
      <input
        v-model="searchQuery"
        type="text"
        placeholder="Buscar libros..."
      />
    </div>

    <!-- Lista de libros -->
    <BookList :searchQuery="searchQuery" @bookSelected="showDetail" />

    <!-- Modal con detalle del libro -->
    <BookDetail v-if="selectedBook" :book="selectedBook" @close="selectedBook = null" />
  </div>
</template>

<script>
import BookList from './components/BookSearch.vue';
import BookDetail from './components/BookDetailsModal.vue';

export default {
  components: { BookList, BookDetail },
  data() {
    return {
      searchQuery: '',
      selectedBook: null,
    };
  },
  methods: {
    showDetail(book) {
      this.selectedBook = book;
    },
  },
};
</script>

<style scoped>
.search-box {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  background: white;
  z-index: 999;
  text-align: center;
  padding: 1rem 0;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.search-box input {
  width: 60%;
  padding: 0.5rem;
  font-size: 1rem;
  border-radius: 8px;
  border: 1px solid #ccc;
}


</style>
