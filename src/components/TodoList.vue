<script setup>
import { computed, ref } from 'vue';
const newTodo = ref('')
const todos = ref([{
        id:  Math.floor(Date.now()),
        title: 'Learn Vue3',
        completed: false
    }])
const visability = ref('all')
const cacheTodo = ref({})
const cacheTitle = ref('')
const filterTodos = computed(() => {
    let newTodos = []
    if(visability.value == 'all') {
        newTodos =  todos.value
    }
    return newTodos
})
</script>

<template>
<div class="container my-3">
  <div class="input-group mb-3">
    <div class="input-group-prepend">
      <span class="input-group-text" id="basic-addon1">待辦事項</span>
    </div>
    <input type="text" class="form-control" placeholder="準備要做的任務" v-model="newTodo" @keyup.enter="addTodo">
    <div class="input-group-append">
      <button class="btn btn-primary" type="button" @click="addTodo">新增</button>
    </div>
  </div>
  <div class="card text-center">
    <div class="card-header">
      <ul class="nav nav-tabs card-header-tabs">
        <li class="nav-item">
          <a class="nav-link" href="#" @click="visability = 'all'" :class="{'active':visability === 'all'}">全部</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#" @click="visability = 'active'" :class="{'active':visability === 'active'}">進行中</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="#" @click="visability = 'completed'" :class="{'active':visability === 'completed'}">已完成</a>
        </li>
      </ul>
    </div>
    <ul class="list-group list-group-flush text-left">
      <li class="list-group-item" v-for="item in filterTodos" @dblclick="editTodo(item)">
        <div class="d-flex" v-if="item.id !== cacheTodo.id">
          <div class="form-check">
            <input type="checkbox" class="form-check-input" id="item.id" v-model="item.completed">
            <label class="form-check-label" for="item.id" :class="{'completed':item.completed}">
              {{item.title}}
            </label>
          </div>
          <button type="button" class="close ml-auto" aria-label="Close" @click="removeTodo(item)">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <input type="text" class="form-control" v-if="item.id === cacheTodo.id" v-model="cacheTitle" @keyup.enter="doneEdit(item)" @keyup.esc="cancelEdit">
      </li>
    </ul>
    <div class="card-footer d-flex justify-content-between">
      <span>還有 {{uncompletedCounter}} 筆任務未完成</span>
      <a href="#" @click="clearAll">清除所有任務</a>
    </div>
  </div>
</div>
</template>