<script setup>
import { ref, onMounted,computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const inputContent = ref('')
const inputCategory = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createDate - a.createDate
}))

const addTodo = () => {
  if (inputContent.value.trim() === '' || inputCategory.value === null) {
    return
  }

  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    done: false,
    createDate: new Date().getTime()
  })

  inputContent.value = ''
  inputCategory.value = null
}

const deleteTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem("todos", JSON.stringify(newVal))
}, { deep: true})

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>
 <main class="app">
  <section class="greeting">
    <h2 class="title">
      Hello there <input type="text" placeholder="Your name" v-model="name">
    </h2>
  </section>

  <section class="create-todo">
    <h3>CREATE A TODO</h3>

    <form @submit.prevent="addTodo">
      <h4>What's on your Todo list?</h4>
      <input
        type="text"
        placeholder="e.g Do Laundry"
        v-model="inputContent"
        autofocus >

      <h4>Pick a category</h4>

      <div class="options">

        <label for="category1">
          <input
            type="radio"
            name="category"
            id="category1"
            value="business"
            v-model="inputCategory" >
          <span class="bubble business"></span>
          <div>Business</div>
        </label>

        <label for="category2">
            <input
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="inputCategory" >
            <span class="bubble personal"></span>
            <div>personal</div>
          </label>
      </div>

      <input type="submit" value="Add Todo">
    </form>
  </section>

  <section class="todo-list">
    <h3>MY TODO LIST</h3>
    <div class="list">

      <div v-for="todo in todos_asc" :class="`todo-item ${ todo.done && 'done' }`" >

        <label>
          <input type="checkbox" v-model="todo.done">
          <span :class="`bubble ${todo.category}`"></span>
        </label>

        <div class="todo-content">
          <input type="text" v-model="todo.content">
        </div>

        <div class="actions">
          <button class="delete" @click="deleteTodo(todo)">Delete</button>
        </div>

      </div>

    </div>
  </section>

 </main>
</template>
