<script setup>
import { computed, ref } from 'vue';
const newTodo = ref('')
const todos = ref([{
  id: Math.floor(Date.now()),
  title: 'Learn Vue3',
  completed: false
}])
const visability = ref('all')
const cacheTodo = ref({})
const cacheTitle = ref('')

const addTodo = () => {
  let value = newTodo.value.trim()
  if (!value) return
  todos.value.push({ id: Math.floor(Date.now()), title: value, completed: false })
  newTodo.value = ''
}

const removeTodo = todo => {
  const index = todos.value.findIndex(value => value.id === todo.id)
  todos.value.splice(index, 1)
}

const editTodo = todo => {
  cacheTodo.value = todo
  cacheTitle.value = todo.title
}

const doneEdit = todo => {
  todo.title = cacheTitle.value
  cacheTitle.value = ''
  cacheTodo.value = {}
}

const cancelEdit = () => {
  cacheTodo.value = {}
}

const clearAll = () => {
  todos.value = []
}

const filterTodos = computed(() => {
  let newTodos = []
  if (visability.value === 'all') {
    newTodos = todos.value
  } else if (visability.value === 'active') {
    todos.value.forEach(todo => {
      if (!todo.completed) newTodos.push(todo)
    })
  } else if (visability.value === 'completed') {
    todos.value.forEach(todo => {
      if (todo.completed) newTodos.push(todo)
    })
  }
  return newTodos
})

const uncompletedCounter = computed(() => {
  let counter = 0
  todos.value.forEach(todo => { if (!todo.completed) counter++ })
  return counter
})
</script>

<template>
  <div class="container my-3">
    <div class="input-group mb-3">
      <div class="input-group-prepend">
        <span class="input-group-text" id="basic-addon1">待辦事項</span>
      </div>
      <input
        type="text"
        class="form-control"
        placeholder="準備要做的任務"
        v-model="newTodo"
        @keyup.enter="addTodo"
      />
      <div class="input-group-append">
        <button class="btn btn-primary" type="button" @click="addTodo">新增</button>
      </div>
    </div>
    <div class="card text-center">
      <div class="card-header">
        <ul class="nav nav-tabs card-header-tabs">
          <li class="nav-item">
            <a
              class="nav-link"
              href="#"
              @click.prevent="visability = 'all'"
              :class="{ 'active': visability === 'all' }"
            >全部</a>
          </li>
          <li class="nav-item">
            <a
              class="nav-link"
              href="#"
              @click.prevent="visability = 'active'"
              :class="{ 'active': visability === 'active' }"
            >進行中</a>
          </li>
          <li class="nav-item">
            <a
              class="nav-link"
              href="#"
              @click.prevent="visability = 'completed'"
              :class="{ 'active': visability === 'completed' }"
            >已完成</a>
          </li>
        </ul>
      </div>
      <ul class="list-group list-group-flush text-left">
        <li class="list-group-item" v-for="item in filterTodos" @dblclick="editTodo(item)">
          <div class="d-flex" v-if="item.id !== cacheTodo.id">
            <div class="form-check">
              <input type="checkbox" class="form-check-input" id="item.id" v-model="item.completed" />
              <label
                class="form-check-label"
                for="item.id"
                :class="{ 'completed': item.completed }"
              >{{ item.title }}</label>
            </div>
            <button
              type="button"
              class="close ml-auto"
              aria-label="Close"
              @click="removeTodo(item)"
            >
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <input
            type="text"
            class="form-control"
            v-if="item.id === cacheTodo.id"
            v-model="cacheTitle"
            @keyup.enter="doneEdit(item)"
            @keyup.esc="cancelEdit"
          />
        </li>
      </ul>
      <div class="card-footer d-flex justify-content-between">
        <span>還有 {{ uncompletedCounter }} 筆任務未完成</span>
        <a href="#" @click.prevent="clearAll">清除所有任務</a>
      </div>
    </div>
  </div>
</template>
<style>
.completed {
  text-decoration: line-through
}
</style>