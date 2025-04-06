<template>
  <div class="book-search">
    <!-- Loader mientras carga -->
    <div v-if="loading" class="loader-container">
      <div class="loader"></div>
      <p>Cargando libros...</p>
    </div>

    <!-- Grid de libros -->
    <div v-else class="book-grid">
      <div
        v-for="book in books"
        :key="book.key"
        class="book"
        @click="selectBook(book)"
      >
        <img
          v-if="book.cover_i"
          :src="`https://covers.openlibrary.org/b/id/${book.cover_i}-M.jpg`"
          alt="Portada del libro"
        />
        <h3>{{ book.title }}</h3>
      </div>

      <!-- Si no hay resultados -->
      <div v-if="books.length === 0 && !loading" class="no-results">
        No se encontraron libros.
      </div>
    </div>

    <!-- Footer con botones de paginación -->
    <div class="pagination-footer" v-if="totalPages > 1">
      <button :disabled="page === 1" @click="changePage(page - 1)">Anterior</button>
      <span>Página {{ page }} de {{ totalPages }}</span>
      <button :disabled="page >= totalPages" @click="changePage(page + 1)">Siguiente</button>
    </div>
  </div>
</template>
  

<script>
export default {
  props: ['searchQuery'],
  data() {
    return {
      page: 1,
      books: [],
      totalPages: 1,
      loading: false,
    };
  },
  watch: {
    searchQuery: {
      handler() {
        this.page = 1;
        this.searchBooks();
      },
      immediate: true,
    },
  },
  methods: {
    selectBook(book) {
      this.$emit('bookSelected', book);
    },
    async searchBooks() {
      this.loading = true;
      this.books = [];

      try {
        if (this.searchQuery && this.searchQuery.trim() !== '') {
          const res = await fetch(
            `https://openlibrary.org/search.json?q=${encodeURIComponent(this.searchQuery)}&page=${this.page}`
          );
          const data = await res.json();
          this.totalPages = Math.ceil(data.numFound / 100);

          const bookList = data.docs.map(book => ({
            ...book,
            description: null,
          }));

          await this.fetchDescriptions(bookList);
        } else {
          const offset = (this.page - 1) * 20;
          const res = await fetch(
            `https://openlibrary.org/subjects/fantasy.json?limit=20&offset=${offset}`
          );
          const data = await res.json();
          this.totalPages = Math.ceil(data.work_count / 20);

          const bookList = data.works.map(book => ({
            ...book,
            title: book.title,
            author_name: book.authors?.map(a => a.name),
            cover_i: book.cover_id,
            key: book.key,
            description: null,
          }));

          await this.fetchDescriptions(bookList);
        }
      } catch (error) {
        console.error('Error al buscar libros:', error);
      } finally {
        this.loading = false;
      }
    },
    async fetchDescriptions(bookList) {
      const promises = bookList.map(async book => {
        if (book.key) {
          try {
            const res = await fetch(`https://openlibrary.org${book.key}.json`);
            const data = await res.json();
            book.description =
              typeof data.description === 'string'
                ? data.description
                : data.description?.value || 'Sin sinopsis disponible.';
          } catch (error) {
            book.description = 'Sin sinopsis disponible.';
          }
        }
      });

      await Promise.all(promises);
      this.books = bookList;
    },
    changePage(newPage) {
      this.page = newPage;
      this.searchBooks();
    },
  },
};
</script>

<style scoped>
.book-search {
  max-width: 800px;
  margin: auto;
  margin-top: 80px;
  padding-bottom: 100px; /* Espacio para el footer */
}

.book-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  justify-content: center;
}

.book {
  cursor: pointer;
  width: 150px;
  text-align: center;
}

.book img {
  width: 100%;
  border-radius: 8px;
}

/* Footer de paginación fijo */
.pagination-footer {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  background-color: #f1f1f1;
  padding: 1rem 0;
  display: flex;
  justify-content: center;
  gap: 1rem;
  align-items: center;
  box-shadow: 0 -2px 5px rgba(0, 0, 0, 0.1);
  z-index: 1000;
}

.pagination-footer button {
  padding: 0.5rem 1rem;
  background-color: #007bff;
  color: white;
  font-weight: bold;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.pagination-footer button:hover:not(:disabled) {
  background-color: #0056b3;
}

.pagination-footer button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

/* Loader animado */
.loader-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 5rem;
}

.loader {
  border: 6px solid #eee;
  border-top: 6px solid #007bff;
  border-radius: 50%;
  width: 60px;
  height: 60px;
  animation: spin 1s linear infinite;
  margin-bottom: 1rem;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>
