<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])

const input_content = ref('')
const input_category =ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}).sort((a, b) => {
  if (a.category === 'priority') {
    return -1
  } else if (a.category === b.category) {
    return 0
  } return 1
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return 
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {deep: true})


onMounted(() => {
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>
  <main class="app max-w-md mx-auto">
    <section class="section create-todo">
      <h3>Create a todo</h3>
      <form @submit="addTodo">
        <h4>What's on your todo list:</h4>
        <input 
          type="text" 
          placeholder="e.g. Do the laundry" 
          v-model="input_content" 
          class="w-full block py-4 px-6 rounded-lg shadow-md mb-6"
        />
        <h4>Pick a category:</h4>
        <div class="grid grid-cols-2 gap-4 mb-6">
          <label>
            <input 
              type="radio" 
              name="category" 
              value="priority" 
              v-model="input_category" />
              <span class="bubble priority"></span>
              <div>Priority</div>
          </label>
          <label>
            <input 
                type="radio" 
                name="category" 
                value="next" 
                v-model="input_category">
                <span class="bubble next"></span>
                <div>Next up</div>
          </label>
        </div>
        <input type="submit" value="Add todo" class="primary-btn block mx-auto"/>
      </form>
    </section>

    <section class="section todo-list">
      <h3>Todo list</h3>
      <div class="my-4">
        <div v-for="todo in todos_asc" :class="`flex items-center p-4 rounded-lg mb-4 shadow-md ${todo.done && 'done'}`">
          <label class="block mr-4 cursor-pointer">
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="flex-1">
            <input type="text" v-model="todo.content">
          </div>

          <button class="delete primary-btn" @click="removeTodo(todo)">Delete</button>
        </div>
      </div>
    </section>
  </main>
</template>

<style scoped>
</style>
