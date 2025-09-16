<!-- <template>
  <div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center">
    <div class="bg-white p-6 rounded shadow w-96">
      <h2 class="text-lg font-bold mb-4">Add New Task</h2>
      <form @submit.prevent="submitTask">
        <input
          v-model="task.text"
          type="text"
          placeholder="Task Title"
          class="w-full p-2 border rounded mb-3"
          required
        />
        <textarea
          v-model="task.description"
          placeholder="Task Description"
          class="w-full p-2 border rounded mb-3"
        ></textarea>
        <div class="flex justify-end space-x-3">
          <button
            type="button"
            @click="$emit('add-task-cancel')"
            class="px-4 py-2 bg-gray-400 text-white rounded"
          >
            Cancel
          </button>
          <button type="submit" class="px-4 py-2 bg-blue-600 text-white rounded">
            Add
          </button>
        </div>
      </form>
    </div>
  </div>
</template> -->

<template>
  <div class="fixed inset-0 bg-black/60 flex items-center justify-center z-50">
    <div class="bg-white p-6 rounded-xl shadow-2xl w-full max-w-md">
      <!-- Title -->
      <h2 class="text-xl font-semibold mb-5 text-gray-800 border-b pb-2">
        Add New Task
      </h2>

      <!-- Form -->
      <form @submit.prevent="submitTask" class="space-y-4">
        <!-- Task Title -->
        <input
          v-model="task.text"
          type="text"
          placeholder="Task Title"
          class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none text-gray-700"
          required
        />

        <!-- Task Description -->
        <textarea
          v-model="task.description"
          placeholder="Task Description"
          rows="4"
          class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none text-gray-700"
        ></textarea>

        <!-- Buttons -->
        <div class="flex justify-end gap-3 pt-2">
          <button
            type="button"
            @click="$emit('add-task-cancel')"
            class="px-5 py-2 rounded-lg border border-gray-300 text-gray-600 hover:bg-gray-100 transition"
          >
            Cancel
          </button>
          <button
            type="submit"
            class="px-5 py-2 rounded-lg bg-blue-600 text-white font-medium hover:bg-blue-700 transition"
          >
            Add Task
          </button>
        </div>
      </form>
    </div>
  </div>
</template>


<script>
export default {
  name: "AddTask",
  data() {
    return {
      task: { text: "", description: "" }
    }
  },
  methods: {
    submitTask() {
      this.$emit("add-task", this.task) // 🔹 send task to BoardSide
      this.task = { text: "", description: "" } // reset
    }
  }
}
</script>

