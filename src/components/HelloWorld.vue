<template>
  <aside class="h-screen w-64 bg-gray-800 text-white flex flex-col p-4">
    <div class="mb-6">
      <h1 class="text-2xl font-bold flex items-center">
        <i class="fa-solid fa-clipboard-list mr-2"></i>
        Kanban
      </h1>
    </div>

    <nav class="space-y-4">
      <!-- Add Task -->
      <button
        @click="$emit('open-add-task')"
        class="flex items-center hover:bg-gray-700 p-2 rounded w-full text-left"
      >
        <i class="fa-solid fa-plus mr-2"></i>
        <span>Add Task</span>
      </button>


    </nav>
  </aside>
</template>

<script>
export default {
  name: "HelloWorld"
}
</script>


