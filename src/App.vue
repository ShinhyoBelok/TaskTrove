<script setup>
  import { ref, onMounted, computed, watch } from 'vue'
  const todos = ref([])
  const name = ref('')

  const in_content = ref('')
  const in_category = ref(null)

  const todos_asc = computed(() => todos.value.sort((a, b) => {
    return a.createdAt - b.createdAt
  }))

  watch(name, (newVal) => {
    localStorage.setItem('name', newVal)
  })
  
  watch(todos, (newVal) => {
    console.log('watch todos');
    localStorage.setItem('todos', JSON.stringify(newVal))
  }, { deep: true })

  const addTodo = () => {
    if (in_content.value.trim() === '' || in_category.value === null) {
      return
    }

    todos.value.push({
      content: in_content.value,
      category: in_category.value,
      done: false,
      createdAt: new Date().getTime()
    })
    
    in_category.value = null
    in_content.value = ''
  }

  const removeTodo = (todo) => {
    todos.value = todos.value.filter(t => t !== todo)
  }

  onMounted(() => {
    name.value = localStorage.getItem('name') || ''
    todos.value = JSON.parse(localStorage.getItem('todos')) || []
  })
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hello, <input type="text" placeholder="Input your name" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TASK LIST</h3>

      <form @submit.prevent="addTodo">
        <h4>What do you want to archive today?</h4>
        <input type="text"
          placeholder="E.g. Go to the GYM at 3pm"
          v-model="in_content" />
          
        <h4>Pick a category</h4>

        <div class="options">
          <label>
            <input type="radio"
              name="category"
              value="business"
              v-model="in_category"/>
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input type="radio"
              name="category"
              value="personal"
              v-model="in_category"/>
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add Task" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TASK LIST</h3>

      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done"/>
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
