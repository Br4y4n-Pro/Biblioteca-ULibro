<template>
  <div class="modal-overlay" @click.self="$emit('close')">
    <div class="modal-content">
      <button class="close-btn" @click="$emit('close')">×</button>
      <div class="modal-body">
        <!-- Imagen -->
        <div class="modal-image">
          <div v-if="loading" class="image-loading">
            <img
              :src="`https://covers.openlibrary.org/b/id/${book.cover_i || book.covers?.[0]}-M.jpg`"
              alt="Portada miniatura"
              class="preview"
            />
            <div class="spinner"></div>
          </div>

          <img
            v-show="!loading"
            :src="`https://covers.openlibrary.org/b/id/${book.cover_i || book.covers?.[0]}-L.jpg`"
            alt="Portada del libro"
            @load="loading = false"
          />
        </div>

        <!-- Texto -->
        <div class="modal-text">
          <h2>{{ book.title }}</h2>
          <p v-if="book.author_name || book.authors"><strong>Autor:</strong>
  {{ (book.author_name || book.authors?.map(a => a.name || a.key).join(', ')) }}
</p>

          <p v-if="book.first_publish_year"><strong>Año de publicación:</strong> {{ book.first_publish_year }}</p>
          <p v-if="book.publish_date"><strong>Fecha de lanzamiento:</strong> {{ book.publish_date[0] }}</p>
          <p v-if="book.edition_count"><strong>Número de ediciones:</strong> {{ book.edition_count }}</p>
          <p v-if="book.language"><strong>Idioma(s):</strong> {{ book.language.join(', ') }}</p>
          <p v-if="book.subject"><strong>Temas:</strong> {{ book.subject.slice(0, 5).join(', ') }}...</p>
          <p v-if="book.subject_places"><strong>Lugares:</strong> {{ book.subject_places.slice(0, 5).join(', ') }}...</p>
          <p v-if="book.subject_people"><strong>Personajes:</strong> {{ book.subject_people.slice(0, 5).join(', ') }}...</p>

          <div class="description" v-if="book.description">
            <p><strong>Sinopsis:</strong></p>
            <p>{{ typeof book.description === 'string' ? book.description : book.description.value }}</p>
          </div>

          <div v-if="book.excerpts && book.excerpts.length">
            <p><strong>Fragmento:</strong></p>
            <blockquote>{{ book.excerpts[0].excerpt }}</blockquote>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['book'],
  data() {
    return {
      loading: true,
    };
  },
};
</script>
<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center; /* centra verticalmente */
  justify-content: center; /* centra horizontalmente */
  z-index: 1000;
}

.modal-content {
  background: white;
  border-radius: 8px;
  max-width: 800px;
  width: 90%;
  max-height: 90vh;
  overflow-y: auto;
  padding: 20px;
  position: relative;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
}

.close-btn {
  position: absolute;
  top: 10px;
  right: 15px;
  font-size: 20px;
  border: none;
  background: transparent;
  cursor: pointer;
}

.modal-body {
  display: flex;
  gap: 20px;
  flex-wrap: wrap;
}

.modal-image {
  display: flex;
  justify-content: center;
  align-items: center;
  min-width: 200px;
}

.modal-image img {
  max-width: 200px;
  border-radius: 4px;
  display: block;
}

.modal-text {
  flex: 1;
  min-width: 200px;
}
</style>
