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
        <button class="px-3 py-1 text-sm font-semibold rounded text-white bg-blue-800">Adicionar</button>
      </form>

      <ul class="w-full py-6">
        <li
          v-for="task of tasks"
          :key="task._id"
          class="flex items-center py-2"
        >
          <span class="flex-1">
            {{ task.text }}
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
export default {
  data() {
    return {
      inputText: '',
      tasks: []
    }
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
      await this.$fetch()
      this.inputText = ''
    },

    async removeTask(task) {
      await this.$axios.$delete(`http://localhost:8080/todos/${task._id}`)
      await this.$fetch()
    }
  }
}
</script>

<style>
html, body, #__nuxt, #__layout {
  height: 100%;
}
</style>
