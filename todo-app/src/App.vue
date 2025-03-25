<script lang="ts">
import {defineComponent, ref, onMounted, watch, computed} from 'vue';

interface Task {
  text: string;
  done: boolean;

}

export default defineComponent({
  setup() {
    const STORAGE_KEY = 'vue-todo-list';

    const newTask = ref<string>('');
    const tasks = ref<Task[]>([]);
    const filter = ref<'all' | 'active' | 'completed'>('all');

    onMounted(() => {
      const saved = localStorage.getItem(STORAGE_KEY)
      if (saved) {
        try {
          tasks.value = JSON.parse(saved)
        } catch (e) {
          console.error('Failed to parse tasks from localStorage', e)
        }
      }
    })

    watch(tasks, (newVal) => {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(newVal))
    }, {deep: true})

    const addTask = () => {
      if (newTask.value.trim() === '') return
      tasks.value.push({text: newTask.value, done: false})
      newTask.value = ''
    }

    const removeTask = (index: number) => {
      tasks.value.splice(index, 1)
    }

    const toggleTask = (index: number) => {
      tasks.value[index].done = !tasks.value[index].done
    }

    const filteredTasks = computed(() => {
      if (filter.value === 'active') {
        return tasks.value.filter(task => !task.done)
      } else if (filter.value === 'completed') {
        return tasks.value.filter(task => task.done)
      }
      return tasks.value
    })

    return {
      newTask,
      tasks,
      addTask,
      removeTask,
      toggleTask,
      filter,
      filteredTasks
    }
  }
})
</script>

<template>
  <div class="container">
    <h1>Todo list</h1>

    <div class="input-container">
      <input v-model="newTask" @keyup.enter="addTask" placeholder="Add task"/>
      <button @click="addTask">Add task</button>
    </div>

    <div class="filters">
      <button @click="filter = 'all'">All</button>
      <button @click="filter = 'active'">Active</button>
      <button @click="filter = 'completed'">Completed</button>
    </div>

    <ul class="list-container">
      <li v-if="filteredTasks.length === 0">
        <p>There are no tasks</p>
      </li>
      <li v-for="(task, index) in filteredTasks" :key="index">
        <div class="task-container" @click="toggleTask(index)">
          <span :class="{done: task.done}" >
            {{ task.text }}
          </span>
          <button @click='removeTask(index)'>x</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<style>
  .container {
    height: 100vh;
    max-width: 500px;
    font-family: sans-serif;
  }

  .input-container {
    display: grid;
    grid-template-columns: 250px 120px;
  }

  input {
    width: 70%;
    padding: 8px;
    margin-right: 10px;
  }

  button {
    padding: 8px 12px;
  }

  li {
    list-style: none;
    margin-top: 10px;
  }

  .done {
    text-decoration: line-through;
    color: gray;
    cursor: pointer;
  }

  span {
    cursor: pointer;
    margin-right: 10px;
  }

  .filters {
    margin: 15px 0;
    display: flex;
    gap: 10px;
  }

  .filters button {
    padding: 6px 12px;
    cursor: pointer;
  }

  .task-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    max-width: 500px;
  }

  ul {
    padding: 5px;
    border: .5px solid gray;
    border-radius: 5px;
  }

  li:hover {
    cursor: pointer;
  }

  li:hover span   {
    color: red;
  }
</style>
