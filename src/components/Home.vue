<template>
  <input type="text" placeholder="添加 todo" v-model="newTodoTitle" @keydown.enter="addTodo" />
  <button @click="addTodo">添加</button>
  <button @click="clearAllDone">清理已完成</button>
  <div v-if="todos.length">
    <div v-for="(todo, index) in todos" :key="todo.title">
      <input type="checkbox" v-model="todo.done" />
      <span>{{ todo.title }}</span>
      <span @click="deleteTodo(index)">❎</span>
    </div>
    <div>
      <span>全选</span>
      <input type="checkbox" v-model="selectAll" />
      <span>{{ hasDone }}/{{ todos.length }}</span>
    </div>
  </div>
  <div v-else>暂无数据</div>
</template>

<script lang="ts" setup>
import { computed, ref } from 'vue';
//定义变量
interface Todo {
  title: string
  done: boolean
}
const newTodoTitle = ref<string>('')
const todos = ref<Todo[]>([
  { title: '吃饭', done: true },
  { title: '睡觉', done: false }
])
const hasDone = computed(() => todos.value.filter(item => item.done).length)  // 已经做了的事项列表

//添加todo
const addTodo = () => {
  if (newTodoTitle.value) {
    todos.value.push({
      title: newTodoTitle.value,
      done: false
    })
    newTodoTitle.value = ''
  }
}
//删除todo
const deleteTodo = (index: number) => {
  todos.value.splice(index, 1)
}

//清除已完成
const clearAllDone = () => {
  todos.value = todos.value.filter(item => !item.done)
}

//选择全部
const selectAll = computed<boolean>({
  get() {
    return hasDone.value === todos.value.length
  },
  set(value: boolean) {
    todos.value.forEach(item => {
      item.done = value
    })
  }
})


</script>


<style scoped>
</style>
