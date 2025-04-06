<template>
  <form @submit.prevent="submitTask" class="task-form">
    <input 
      v-model="taskDescription" 
      placeholder="Nueva tarea" 
      required 
      class="task-input" 
    />
    <button type="submit" class="task-submit-btn">Agregar</button>
  </form>
</template>

<script>
export default {
  data() {
    return {
      taskDescription: '',
    };
  },
  methods: {
    submitTask() {
      if (this.taskDescription.trim()) {
        this.$emit('add-task', { description: this.taskDescription });
        this.taskDescription = ''; // Limpiar el input después de agregar
      }
    },
  },
};
</script>
