<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todo_asc = computed(() => todos.value.sort((a, b) => {
  return b.createAt - a.createAt;
}))

watch(name, (val) => {
  localStorage.setItem('name', val)
})
watch(todos, (val) => {
  localStorage.setItem('todos', JSON.stringify(val))
}, { deep: true })
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
function addTodo() {
  if (input_content.value.trim() === '' || input_category.value == null) return
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createAt: new Date().getTime()
  })

}
function deleteTodo(createAt) {
  todos.value=todos.value.filter(todos => todos.createAt!=createAt);
}
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Whats Up <input type="text" placeholder="Name here" v-model="name">
      </h2>
    </section>
    <section class="create-todo">
      <h3>CREATE TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>Whats on your todo list ?</h4>
        <input type="text" placeholder="e.g. make a video" v-model="input_content">
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="input_category">
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input type="radio" name="category" value="personal" v-model="input_category">
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input value="Add Todo" type="submit">
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todo_asc " :key="todo.createAt" :class="`todo-item ${todo.done&&'done'}`">
          <label >
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>
          <div class="actions">
            <button class="delete" @click="deleteTodo(todo.createAt)">Delete</button>
          </div>
        </div>

      </div>
    </section>
  </main>
</template>
 
