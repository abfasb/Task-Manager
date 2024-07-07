<template>
  <div>
    <h1>Task Manager</h1>
    <ul>
      <li v-for="task in tasks" :key="task.id">
        {{ task.title }} - {{ task.status }}
        <button @click="toggleStatus(task)">Toggle Status</button>
        <button @click="deleteTask(task.id)">Delete</button>
      </li>
    </ul>
    <form @submit.prevent="addTask">
      <input v-model="newTaskTitle" placeholder="Task title" />
      <select v-model="newTaskStatus">
        <option value="pending">Pending</option>
        <option value="completed">Completed</option>
      </select>
      <button type="submit">Add Task</button>
    </form>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';

export default defineComponent({
  name: 'TaskManager',
  setup() {
    const tasks = ref([]);
    const newTaskTitle = ref('');
    const newTaskStatus = ref('pending');

    const fetchTasks = async () => {
      const response = await fetch('http://localhost:8080/api/tasks');
      tasks.value = await response.json();
    };

    const addTask = async () => {
      const newTask = {
        title: newTaskTitle.value,
        status: newTaskStatus.value,
      };
      await fetch('http://localhost:8080/api/tasks', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(newTask),
      });
      fetchTasks();
      newTaskTitle.value = '';
      newTaskStatus.value = 'pending';
    };

    const toggleStatus = async (task) => {
      const updatedTask = { ...task, status: task.status === 'pending' ? 'completed' : 'pending' };
      await fetch(`http://localhost:8080/api/tasks/${task.id}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(updatedTask),
      });
      fetchTasks();
    };

    const deleteTask = async (id) => {
      await fetch(`http://localhost:8080/api/tasks/${id}`, {
        method: 'DELETE',
      });
      fetchTasks();
    };

    fetchTasks();

    return {
      tasks,
      newTaskTitle,
      newTaskStatus,
      addTask,
      toggleStatus,
      deleteTask,
    };
  },
});
</script>

<style scoped>
/* This is the style part you can add the style here based on your preference :>*/
</style>
