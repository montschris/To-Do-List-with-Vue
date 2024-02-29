<template>
  <div class="wrapper">
    <header>
      <h1 class="header-title">To-Do List</h1>
    </header>
    <section>
      <div class="task-input">
        <input v-model="tempText" type="text" placeholder="Enter Task" class="task-input-field">
        <button @click="addTask" class="add-task-button">Add Task</button>
      </div>
      <ul>
        <li v-for="todo in todosFormatted" :key="todo.id" :id="'task-' + todo.id">
          <input type="checkbox" v-model="todo.done" class="task-checkbox">
          <span :class="{ finishedTask: todo.done }" @dblclick="editTask(todo)" class="task-text">{{ todo.text }}</span>
          <button class="delete-task" @click="removeTask(todo)"><i class="fas fa-trash-alt"></i></button>
          <button class="rename-task" @click="showRenameDialog(todo)"><i class="fas fa-pencil-alt"></i></button>
        </li>
        <button class="toggle-tasks" @click="toggleHideFinishedTasks">{{ hideFinishedTasks ? 'Show Finished Tasks' : 'Hide Finished Tasks' }}</button>
      </ul>
    </section>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

let id = 0;
const tempText = ref('');
const hideFinishedTasks = ref(false);
const todos = ref(JSON.parse(localStorage.getItem('todos')) || []);

function addTask() {
  if (tempText.value !== '') {
    todos.value.push({ id: id++, text: tempText.value, done: false });
    tempText.value = '';
    updateLocalStorage();
  }
}

function removeTask(task) {
  todos.value = todos.value.filter(todo => todo !== task);
  updateLocalStorage();
}

function editTask(task) {
  const newText = prompt('Enter new task name:', task.text);
  if (newText !== null) {
    task.text = newText.trim();
    updateLocalStorage();
  }
}

function showRenameDialog(task) {
  const newText = prompt('Enter new task name:', task.text);
  if (newText !== null) {
    task.text = newText.trim();
    updateLocalStorage();
  }
}

function updateLocalStorage() {
  localStorage.setItem('todos', JSON.stringify(todos.value));
}

const todosFormatted = computed(() => hideFinishedTasks.value ? todos.value.filter(todo => !todo.done) : todos.value);
const toggleHideFinishedTasks = () => (hideFinishedTasks.value = !hideFinishedTasks.value);

onMounted(() => {
  window.addEventListener('beforeunload', () => {
    updateLocalStorage();
  });
});
</script>

<style scoped>
@import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css');

.wrapper {
  max-width: 600px;
  margin: 0 auto;
  padding: 30px;
  border-radius: 30px;
  background-color: #f5f5f5;
  box-shadow: 0 0 20px rgba(28, 24, 24, 0.2);
}

header {
  text-align: center;
  margin-bottom: 20px;
}

.header-title {
  font-size: 2.5rem;
  font-weight: bold;
  color: #333;
  background: linear-gradient(to right, #ff7e5f, #feb47b);
  background-clip: text;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

mode-toggle {
  background: none;
  border: none;
  cursor: pointer;
}

section {
  padding: 20px;
  border-top: 2px solid #ddd;
}

.task-input {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.task-input input {
  flex: 1;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 1.2rem;
}

.task-input button {
  background-color: #27435c;
  color: #fff;
  border: none;
  border-radius: 5px;
  padding: 10px 20px;
  margin-left: 10px;
  cursor: pointer;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.task-checkbox {
  margin-right: 15px;
}

.task-text {
  flex: 1;
  font-size: 1.1rem;
  color: #333;
  margin-right: 10px;
}

.finishedTask {
  text-decoration: line-through;
  color: #666;
}

.delete-task, .rename-task {
  background-color: #b15e54;
  color: #fff;
  border: none;
  border-radius: 5px;
  padding: 10px 16px;
  margin-left: auto;
  cursor: pointer;
  transition: background-color 0.5s ease;
}

.delete-task:hover, .rename-task:hover {
  background-color: #c0392b;
}

.rename-task {
  margin-left: 10px;
}

.toggle-tasks {
  background-color: #637788;
  color: #fff;
  border: none;
  border-radius: 5px;
  padding: 12px 24px;
  margin-top: 20px;
  cursor: pointer;
}
</style>
