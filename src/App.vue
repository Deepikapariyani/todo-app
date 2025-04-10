<script setup>
import { onMounted, ref, onUnmounted } from 'vue'
import { watch } from 'vue'
import { computed } from 'vue'

const todoText = ref('')
const weekday = ref(new Date().toString().slice(0, 3))
const todos = ref([])
const searchText = ref('')

const filteredTodos = computed(() => {
  return todos.value.filter((todo) =>
    todo.text.toLowerCase().includes(searchText.value.toLowerCase()),
  )
})

watch(
  todos,
  (newTodos) => {
    localStorage.setItem('todos', JSON.stringify(newTodos))
  },
  { deep: true },
)
onMounted(() => {
  const storedTodos = localStorage.getItem('todos')
  console.log(storedTodos)
  if (storedTodos) {
    todos.value = JSON.parse(storedTodos)
  } else {
    localStorage.setItem('todos', JSON.stringify([]))
  }
})
const addTodo = () => {
  if (todoText.value !== '') {
    todos.value.push({
      text: todoText.value,
      wDay: weekday.value,
      completed: false,
    })
    todoText.value = ''
  }
}
const toggleDone = (index) => {
  todos.value[index].completed = !todos.value[index].completed
}
const removeItem = (index) => {
  todos.value.splice(index, 1)
}
</script>

<template>
  <main class="main">
    <div class="todo-wrapper">
      <form class="todo-form" @submit.prevent="addTodo">
        <div class="inputs">
          <div class="form-floating form-group flex-fill">
            <input class="form-control" id="title" v-model="todoText" placeholder="What to do?" />
            <label for="title">Title</label>
          </div>

          <div class="form-floating ms-2">
            <select
              v-model="weekday"
              class="form-select"
              id="floatingSelect"
              aria-label="Floating label select example"
            >
              <option value="Mon">Monday</option>
              <option value="Tue">Tuesday</option>
              <option value="Wed">Wednesday</option>
              <option value="Thu">Thursday</option>
              <option value="Fri">Friday</option>
              <option value="Sat">Saturday</option>
              <option value="Sun">Sunday</option>
            </select>
            <label for="floatingSelect">Weekday</label>
          </div>
        </div>
        <button class="add-btn btn btn-outline-primary rounded-3">Add Task</button>
      </form>
    </div>

    <div v-if="todos.length > 0" class="wrapper">
      <div class="ps-2 pe-2">
        <div class="form-floating form-group flex-fill">
          <input class="form-control" id="search" v-model="searchText" placeholder="What to do?" />
          <label for="search">Search tasks...</label>
        </div>
      </div>
      <div id="todo-app">
        <div style="margin: 20px">
          <table class="table table-striped">
            <tbody>
              <tr v-for="(todo, index) in filteredTodos" :key="todo.id">
                <td>
                  <input type="checkbox" :checked="todo.completed" @change="toggleDone(index)" />
                </td>

                <td
                  :class="todo.completed ? 'completed' : ''"
                  style="
                    text-align: left;
                    max-width: 8rem;
                    white-space: nowrap;
                    overflow: hidden;
                    text-overflow: ellipsis;
                  "
                >
                  {{ todo.text }}
                </td>
                <td :class="todo.completed ? 'completed' : ''">{{ todo.wDay }}</td>

                <td>
                  <button
                    type="button"
                    class="btn-close"
                    style="height: 0.2rem; width: 0.2rem"
                    aria-label="Close"
                    @click="removeItem(index)"
                  ></button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </main>
</template>
