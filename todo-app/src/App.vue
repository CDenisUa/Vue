<script lang="ts">
import {defineComponent, ref} from 'vue';

interface Task {
  text: string;
  done: boolean;
}

export default defineComponent({
  setup() {
    const newTask = ref<string>('')
    const tasks = ref<Task[]>([])

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

    return {
      newTask,
      tasks,
      addTask,
      removeTask,
      toggleTask
    }
  }
})
</script>

<template>
  <div class="container">
    <h1>Todo list</h1>

    <input v-model="newTask" @keyup.enter="addTask" placeholder="Add task"/>
    <button @click="addTask">Add task</button>

    <ul>
      <li v-for="(task, index) in tasks" :key="index">
      <span :class="{done: task.done}" @click="toggleTask(index)">
        {{ task.text }}
      </span>
        <button @click='removeTask(index)'>x</button>
      </li>
    </ul>
  </div>
</template>

<style>
.container {
  max-width: 500px;
  margin: auto;
  padding: 20px;
  font-family: sans-serif;
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
</style>
