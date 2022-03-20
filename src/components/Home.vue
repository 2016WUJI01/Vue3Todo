<template>
  <input
    type="text"
    placeholder="添加 todo"
    v-model="newTodoValue"
    @keydown.enter="createtodo(newTodoValue)"
  />
  <button @click="createtodo(newTodoValue)">添加</button>
  <div v-if="todoList.length">
    <div v-for="todo in todoList" :key="todo.id">
      <input type="checkbox" v-model="todo.completed" />
      {{ todo.value }}
      <span @click="removetodo(todo)">❎</span>
    </div>
    <div>{{ remaining }}/{{ todoList.length }}</div>
  </div>
  <div v-else>暂无数据</div>
</template>

<script lang="ts" setup>
import { computed, ref, watch } from 'vue';
//定义变量
interface todo {
  id: number
  value: string
  completed?: boolean
}
interface filters {
  [key: string]: (todo: todo[]) => todo[]
}
const newTodoValue = ref<string>('')

const STORAGE_KEY = 'vue3todo'
const todoStorage = {
  fetch() {
    const todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]')
    return todos
  },
  save(todos: todo[]) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos))
  }
}
const todoList = ref<todo[]>(todoStorage.fetch())

const filters: filters = {
  all(todos) {
    return todos
  },
  active(todos) {
    return todos.filter((todo) => !todo.completed)
  },
  computed(todos) {
    return todos.filter((todo) => todo.completed)
  },
}

const remaining = computed(() => filters.active(todoList.value).length)
watch(() => todoList.value, (todos) => {
  todoStorage.save(todos)
}, {
  deep: true
})

const createtodo = (value: string) => {
  if (value) {
    todoList.value.push({
      id: new Date().getTime(),
      value: value,
      completed: false
    })
  }
}

const removetodo = (todo: todo) => {
  todoList.value = todoList.value.filter(item => item.id !== todo.id)
}

</script>


<style scoped>
</style>
