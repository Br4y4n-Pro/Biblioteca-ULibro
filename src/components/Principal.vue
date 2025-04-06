<template>
    <div class="book-search">
      <input v-model="query" @keyup.enter="searchBooks" placeholder="Buscar libros..." />
  
      <div v-if="books.length === 0 && loading">Cargando libros...</div>
      <div v-else>
        <div v-for="book in books" :key="book.key" class="book">
          <h3>{{ book.title }}</h3>
          <p><strong>Autor:</strong> {{ book.author_name?.join(', ') }}</p>
          <img
            v-if="book.cover_i"
            :src="`https://covers.openlibrary.org/b/id/${book.cover_i}-M.jpg`"
            alt="Portada del libro"
          />
        </div>
  
        <div class="pagination">
          <button :disabled="page === 1" @click="page-- && searchBooks()">Anterior</button>
          <span>Página {{ page }}</span>
          <button :disabled="page >= totalPages" @click="page++ && searchBooks()">Siguiente</button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        query: 'harry potter',
        page: 1,
        books: [],
        totalPages: 1,
        loading: false,
      };
    },
    methods: {
      async searchBooks() {
        this.loading = true;
        const res = await fetch(
          `https://openlibrary.org/search.json?q=${encodeURIComponent(this.query)}&page=${this.page}`
        );
        const data = await res.json();
  
        this.books = data.docs;
        this.totalPages = Math.ceil(data.numFound / 100); // API devuelve hasta 100 por página
        this.loading = false;
      },
    },
    mounted() {
      this.searchBooks();
    },
  };
  </script>
  
  <style>
  .book-search {
    max-width: 600px;
    margin: auto;
  }
  .book {
    border-bottom: 1px solid #ccc;
    margin-bottom: 1rem;
  }
  .pagination {
    display: flex;
    justify-content: space-between;
    margin-top: 1rem;
  }
  </style>
  