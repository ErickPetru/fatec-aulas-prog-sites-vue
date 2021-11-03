<template>
  <div class="grid p-4 place-items-center h-screen">
    <div class="w-full">
      <h1 class="text-center font-bold">Editar Tarefa</h1>

      <form
        class="flex w-full max-w-screen-xl mt-6 space-x-2"
        @submit.prevent="updateTask"
      >
        <input
          v-model="taskText"
          placeholder="Texto da tarefa"
          class="flex-1 px-2 py-1 border border-gray-500 rounded outline-none"
        >

        <label>
          <input v-model="taskDone" type="checkbox">
          <span>Tarefa concluída</span>
        </label>

        <button
          class="px-3 py-1 text-sm font-semibold rounded text-white bg-blue-800"
        >
          Atualizar
        </button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      taskText: '',
      taskDone: false
    }
  },

  async fetch() {
    const _id = this.$route.params.id
    const task = await this.$axios.$get(`http://localhost:8080/todos/${_id}`)
    this.taskText = task.text
    this.taskDone = task.done
  },

  methods: {
    async updateTask() {
      const task = {
        text: this.taskText,
        done: this.taskDone,
      }

      const _id = this.$route.params.id
      await this.$axios.$put(`http://localhost:8080/todos/${_id}`, task)
      this.$router.push('/')
    }
  }
}
</script>
