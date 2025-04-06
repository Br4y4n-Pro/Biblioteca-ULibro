<template>
  <div class="caja-principal">
    <div class="sticky">
      <h1>Gestor de Tareas Ágil</h1>
      <!-- Componente de formulario para agregar tareas -->
      <task-form @add-task="addTask" />

      <!-- Botones de filtro para tareas -->
      <div class="filters">
        <button
          :class="{ active: filter === 'all' }"
          @click="filterTasks('all')"
        >
          Todas
        </button>
        <button
          :class="{ active: filter === 'completed' }"
          @click="filterTasks('completed')"
        >
          Completadas
        </button>
        <button
          :class="{ active: filter === 'pending' }"
          @click="filterTasks('pending')"
        >
          Pendientes
        </button>
      </div>
    </div>

    <!-- Lista de tareas -->
    <div class="task-list-container">
      <ul>
        <task-item
          v-for="task in filteredTasks"
          :key="task.id"
          :task="task"
          @delete-task="deleteTask"
          @toggle-completed="toggleCompleted"
        />
      </ul>
      <!-- Mensaje si no hay tareas que mostrar -->
      <p v-if="filteredTasks.length === 0">No hay tareas en esta categoría.</p>
    </div>
  </div>
</template>

<script>
import TaskForm from './TaskForm.vue';
import TaskItem from './TaskItem.vue';

export default {
  components: {
    TaskForm,
    TaskItem,
  },
  data() {
    return {
      tasks: this.loadTasksFromLocalStorage(), // Cargar tareas al iniciar
      filter: 'all', // Filtro predeterminado
    };
  },
  computed: {
    filteredTasks() {
      // Filtrar las tareas según el filtro seleccionado
      if (this.filter === 'completed') {
        return this.tasks.filter((task) => task.completed);
      } else if (this.filter === 'pending') {
        return this.tasks.filter((task) => !task.completed);
      }
      return this.tasks; // Si el filtro es "all", devolver todas las tareas
    },
  },
  methods: {
    addTask(newTask) {
      // Crear una nueva tarea y agregarla a la lista
      const task = {
        id: Date.now(),
        description: newTask.description,
        createdAt: new Date(),
        completed: false, // Estado inicial
      };
      this.tasks.unshift(task); // Agregar la nueva tarea al principio
      this.saveTasksToLocalStorage(); // Guardar en localStorage
    },
    deleteTask(taskId) {
      // Eliminar una tarea por su ID
      this.tasks = this.tasks.filter((task) => task.id !== taskId);
      this.saveTasksToLocalStorage(); // Guardar cambios en localStorage
    },
    toggleCompleted(taskId) {
      // Cambiar el estado de completado de una tarea
      const task = this.tasks.find((task) => task.id === taskId);
      if (task) {
        task.completed = !task.completed;
        this.saveTasksToLocalStorage(); // Guardar cambios en localStorage
      }
    },
    filterTasks(status) {
      // Cambiar el filtro según el botón seleccionado
      this.filter = status;
    },
    saveTasksToLocalStorage() {
      // Guardar tareas en localStorage
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    },
    loadTasksFromLocalStorage() {
      // Cargar tareas desde localStorage
      const tasks = localStorage.getItem('tasks');
      return tasks ? JSON.parse(tasks) : [];
    },
  },
};
</script>

<style scoped>
.caja-principal {
  padding: 20px;
  font-family: Arial, sans-serif;
}

.sticky {
  position: sticky;
  top: 0;
  background-color: #f8f9fa;
  padding: 15px;
  border-bottom: 2px solid #ccc;
  z-index: 10;
}

.filters {
  margin-top: 10px;
  margin-bottom: 20px;
}

.filters button {
  margin-right: 10px;
  padding: 5px 10px;
  cursor: pointer;
  background-color: #e9ecef;
  border: 1px solid #ddd;
  border-radius: 5px;
}

.filters button.active {
  background-color: #007bff;
  color: white;
  font-weight: bold;
}

.task-list-container {
  margin-top: 20px;
}

.task-completed .task-description {
  text-decoration: line-through;
  color: #888;
}

.task-completed {
  background-color: #d3ffd3; /* Fondo verde claro para tareas completadas */
}

.task-checkbox {
  margin-right: 10px;
}

.delete-btn {
  color: red;
  background: none;
  border: none;
  cursor: pointer;
}
</style>
