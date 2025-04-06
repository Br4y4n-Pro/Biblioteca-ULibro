<template>
  <li :class="{ 'task-completed': task.completed }" class="task-item">
    <div class="task-content">
      <!-- Checkbox con vinculación manual en lugar de v-model -->
      <input
        type="checkbox"
        :checked="task.completed"
        @change="emitCompletedStatusChange"
        class="task-checkbox"
      />
      <span class="task-description">{{ task.description }}</span>
      <span v-if="task.createdAt" class="task-date">
        Creada: {{ formatDate(task.createdAt) }}
      </span>
    </div>
    <button @click="deleteTask" class="delete-btn">
      <i class="fas fa-trash-alt"></i>
    </button>
  </li>
</template>

<script>
export default {
  props: {
    task: Object, // Recibe la tarea como propiedad
  },
  methods: {
    // Emitir el cambio de estado de completado al componente padre
    emitCompletedStatusChange() {
      this.$emit('toggle-completed', this.task.id); // Emitir el ID de la tarea para actualizar el estado
    },
    // Emitir la solicitud de eliminación al componente padre
    deleteTask() {
      this.$emit('delete-task', this.task.id); // Emitir el ID de la tarea para eliminarla
    },
    // Método para dar formato a la fecha de creación
    formatDate(date) {
      return new Date(date).toLocaleString(); // Formato de fecha local
    },
  },
};
</script>

<style scoped>
.task-checkbox {
  margin-right: 10px;
}

.task-completed .task-description {
  text-decoration: line-through;
  color: #888;
}
</style>
