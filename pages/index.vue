<template>
  <div class="grid p-4 place-items-center h-screen">
    <div class="w-full">
      <h1 class="text-center font-bold">Tarefas</h1>

      <form
        class="flex w-full max-w-screen-xl mt-6 space-x-2"
        @submit.prevent="addTask"
      >
        <input
          v-model="inputText"
          placeholder="O que precisa ser feito?"
          class="flex-1 px-2 py-1 border border-gray-500 rounded outline-none"
        >

        <button
          class="px-3 py-1 text-sm font-semibold rounded text-white bg-blue-800"
        >
          Adicionar
        </button>
      </form>

      <ul class="w-full py-6">
        <li
          v-for="task of tasks"
          :key="task._id"
          class="flex items-center py-2"
        >
          <span class="flex-1">
            <nuxt-link
              :to="`/tasks/${task._id}`"
              :class="{
                'line-through text-gray-400': task.done,
                'text-blue-800': !task.done
              }"
            >
              {{ task.text }}
            </nuxt-link>
          </span>

          <button
            class="px-3 py-1 text-sm font-semibold rounded text-white bg-red-800 uppercase"
            @click="removeTask(task)"
          >
            x
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import io from 'socket.io-client'

export default {
  data() {
    return {
      inputText: '',
      tasks: []
    }
  },

  mounted() {
    const socket = io('http://localhost:8080')

    socket.on('todo-created', (serverTask) => {
      this.tasks.push(serverTask)
    })

    socket.on('todo-updated', (serverTask) => {
      const localTask = this.tasks.find((localTask) =>
        localTask._id === serverTask._id
      )
      localTask.text = serverTask.text
      localTask.done = serverTask.done
    })

    socket.on('todo-removed', (serverTask) => {
      this.tasks = this.tasks.filter((localTask) =>
        localTask._id !== serverTask._id
      )
    })
  },

  async fetch() {
    this.tasks = await this.$axios.$get('http://localhost:8080/todos')
  },

  methods: {
    async addTask() {
      const task = {
        text: this.inputText,
        locale: 'pt'
      }

      await this.$axios.$post('http://localhost:8080/todos', task)
      this.inputText = ''
    },

    async removeTask(task) {
      await this.$axios.$delete(`http://localhost:8080/todos/${task._id}`)
    }
  }
}
</script>

<style>
html, body, #__nuxt, #__layout {
  height: 100%;
}
</style>
