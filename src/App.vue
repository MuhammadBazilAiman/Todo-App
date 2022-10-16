<script setup>
import { ref, onMounted, watch } from "vue"

const todos = ref([])
const name = ref("")

const input_content = ref("")
const input_category = ref(null)

const deleted_todo = ref([])
const toggle_this = ref(false)

// sort the todos based on date created
// const todos_asc = computed(() => todos.value.sort((a, b) => {
//   return b.createdAt - a.createdAt
// }))

const important_todo = (todo) => {
  if (!todo.important) {
    todo.important = true
    todos.value = todos.value.filter(t => t !== todo)
    todos.value.unshift(todo)
  } else {
    todo.important = false
  }
}

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
    important: false
  })

  input_content.value = ""
  input_category.value = null
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
  deleted_todo.value.push(todo)
}

const undoTodo = deleted => {
  todos.value.push(deleted)
  deleted_todo.value = deleted_todo.value.filter(d => d !== deleted)
}

// watch for changes in name
watch(name, (newVal) => {
  localStorage.setItem("name", newVal)
})

// watch for changes in todos array
watch(todos, (newVal) => {
  localStorage.setItem("todos", JSON.stringify(newVal))
}, { deep: true })
// deep checks for changes in every line of todos

// call data from local storage 
onMounted(() => {
  name.value = localStorage.getItem("name") || ""
  todos.value = JSON.parse(localStorage.getItem("todos")) || []
})

</script>
  
<template>
  <main id="app">
    <section class="greeting">
      <h2 class="title">
        Hello, <input type="text" placeholder="Name here" v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input type="text" placeholder="example: read a book" v-model="input_content">

        <h4>Pick a category</h4>

        <div class="options">

          <label class="business">
            <input type="radio" name="category" value="business" v-model="input_category">
            <div>Business</div>
          </label>

          <label class="personal">
            <input type="radio" name="category" value="personal" v-model="input_category">
            <div>Personal</div>
          </label>

        </div>

        <input type="submit" value="Add todo">
      </form>

    </section>

    <section class="todo-list">

      <div class="category-color">
        <div class="business-category">Business</div>
        <div class="personal-category">Personal</div>
        <div class="important-category">Important</div>
      </div>

      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todos" :class="`todo-item ${todo.done && 'done'} ${todo.important && 'important'}`">

          <label>
            <input type="checkbox" v-model="todo.done">
          </label>

          <div class="todo-content" :class="`bubble ${todo.category}`">
            <input type="text" v-model="todo.content">
          </div>
          <div class="actions">
            <button @click="important_todo(todo)">Important</button>
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>

        </div>
      </div>
    </section>

    <label class="deleted-todo-box">
      Show deleted todo
      <input type="checkbox" value="Show deleted todo" v-model="toggle_this">
    </label>

    <section class="deleted-todo-list-box" :class="`${toggle_this && 'view-todo'}`">

      <div class="deleted-todo-list" v-for="deleted in deleted_todo">
        {{deleted.content}}
        <button @click="undoTodo(deleted)">Undo</button>
      </div>

    </section>

    <footer>
      <p>by Muhammad Bazil Aiman &#127801</p>
    </footer>

  </main>
</template>
