<template>
  <div class="flex">
    <!-- Sidebar -->
    <HelloWorld
      @open-add-task="openAddTask"
      @toggle-delete-mode="toggleDeleteMode"
    />

    <!-- Main Board -->
    <div class="flex flex-col gap-6 p-6 w-full">
      <!-- Add Task Modal -->
      <AddTask
        v-if="showAddTask"
        @add-task="saveTask"
        @add-task-cancel="closeAddTask"
      />

      <!-- Edit Task Modal -->
      <div
        v-if="showEditTask"
        class="fixed inset-0 bg-black/60 flex items-center justify-center z-50"
      >
        <div class="bg-white p-6 rounded-xl shadow-2xl w-full max-w-md">
          <h2 class="text-xl font-semibold mb-5 text-gray-800 border-b pb-2">Edit Task</h2>

          <form @submit.prevent="updateTask" class="space-y-4">
            <input
              v-model="editForm.text"
              type="text"
              class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none"
              required
            />
            <textarea
              v-model="editForm.description"
              rows="4"
              class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:outline-none"
            ></textarea>

            <div class="flex justify-end gap-3 pt-2">
              <button
                type="button"
                @click="cancelEdit"
                class="px-4 py-2 border border-gray-300 rounded-lg text-gray-600 hover:bg-gray-100"
              >
                Cancel
              </button>
              <button
                type="submit"
                class="px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700"
              >
                Save
              </button>
            </div>
          </form>
        </div>
      </div>

      <!-- Kanban Columns -->
      <div class="flex gap-6">
        <div
          v-for="(column, colIndex) in columns"
          :key="colIndex"
          class="w-72 bg-white rounded shadow p-4"
        >
          <!-- Column Header with color -->
          <h2 
            class="font-bold mb-4 text-white py-2 px-3 rounded"
            :class="getColumnColor(column.name)"
          >
            {{ column.name }}
          </h2>

          <!-- Sortable target -->
          <div
            class="task-list min-h-[56px] space-y-2"
            :data-col-index="colIndex"
          >
            <div
              v-for="(task, index) in column.tasks"
              :key="task.id"
              class="task-card border p-3 rounded flex flex-col bg-white shadow-sm"
              :class="getCardColor(column.name)"
            >
              <div class="flex items-center gap-2 mb-2">
              <!-- Drag button -->
              <button
              type="button"
              class="drag-handle flex items-center justify-center text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:ring-blue-300 font-medium rounded-lg text-sm h-10 w-28"
              >
              Drag & Drop
              </button>

              <!-- Edit button -->
             <button 
             type="button"
             @click="editTask(colIndex, index)"
             class="flex items-center justify-center text-white bg-green-700 hover:bg-green-800 focus:ring-4 focus:ring-green-300 font-medium rounded-lg text-sm h-10 w-10"
             >
             <i class="fas fa-edit"></i>
             </button>

            <!-- Delete button -->
             <button 
             @click="deleteTask(colIndex, index)" 
             class="flex items-center justify-center text-white bg-red-700 hover:bg-red-800 focus:ring-4 focus:ring-red-300 font-medium rounded-lg text-sm h-10 w-10"
             title="Delete"
             >
             <i class="fa-solid fa-trash"></i>
             </button>
            </div>

              
              <div class="pr-2">
                <span class="font-semibold text-gray-800">{{ task.text }}</span>
                <p v-if="task.description" class="text-sm text-gray-500 break-words whitespace-pre-line mt-1">
                  {{ task.description }}
                </p>
              </div>
            </div>
          </div>

          <!-- Empty state -->
          <p v-if="column.tasks.length === 0" class="text-gray-400 text-sm mt-2">
            No tasks yet
          </p>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import AddTask from "./AddTask.vue"
import HelloWorld from "./HelloWorld.vue"
import Sortable from "sortablejs"
import { nextTick } from "vue"

export default {
  name: "BoardSide",
  components: { AddTask, HelloWorld },
  data() {
    return {
      showAddTask: false,
      showEditTask: false,
      deleteMode: false,

      editing: { colIndex: null, taskIndex: null },
      editForm: { text: "", description: "" },

      columns: [
        { name: "To Do List", tasks: [] },
        { name: "Requested", tasks: [] },
        { name: "In Progress", tasks: [] },
        { name: "Done", tasks: [] }
      ],

      sortables: []
    }
  },
  mounted() {
    this.loadFromLocalStorage()       // ✅ Load saved tasks
    nextTick(() => this.initSortable())
  },
  methods: {
    // 🔹 LocalStorage helpers
    saveToLocalStorage() {
      localStorage.setItem("kanban-columns", JSON.stringify(this.columns))
    },
    loadFromLocalStorage() {
      const saved = localStorage.getItem("kanban-columns")
      if (saved) {
        this.columns = JSON.parse(saved)
      }
    },

    // 🔹 Task actions
    openAddTask() { this.showAddTask = true },
    closeAddTask() { this.showAddTask = false },

    saveTask(task) {
      const todo = this.columns.find(c => c.name === "To Do List")
      if (todo) {
        todo.tasks.push({
          id: Date.now(),
          text: task.text,
          description: task.description
        })
      }
      this.showAddTask = false
      this.saveToLocalStorage()   // ✅ Save
    },

    toggleDeleteMode() {
      this.deleteMode = !this.deleteMode
    },
    deleteTask(colIndex, taskIndex) {
      this.columns[colIndex].tasks.splice(taskIndex, 1)
      this.saveToLocalStorage()   // ✅ Save
    },

    // 🔹 Edit task
    editTask(colIndex, taskIndex) {
      this.editing = { colIndex, taskIndex }
      const task = this.columns[colIndex].tasks[taskIndex]
      this.editForm = { text: task.text, description: task.description }
      this.showEditTask = true
    },
    updateTask() {
      const { colIndex, taskIndex } = this.editing
      this.columns[colIndex].tasks[taskIndex].text = this.editForm.text
      this.columns[colIndex].tasks[taskIndex].description = this.editForm.description
      this.cancelEdit()
      this.saveToLocalStorage()   // ✅ Save
    },
    cancelEdit() {
      this.showEditTask = false
      this.editing = { colIndex: null, taskIndex: null }
      this.editForm = { text: "", description: "" }
    },

    // 🔹 Color helpers
    getColumnColor(name) {
      switch (name) {
        case "To Do List": return "bg-gray-600"
        case "Requested": return "bg-blue-600"
        case "In Progress": return "bg-yellow-500"
        case "Done": return "bg-green-600"
        default: return "bg-gray-400"
      }
    },
    getCardColor(name) {
      switch (name) {
        case "To Do List": return "bg-gray-100 border-gray-600"
        case "Requested": return "bg-blue-50 border-blue-500"
        case "In Progress": return "bg-yellow-50 border-yellow-500"
        case "Done": return "bg-green-50 border-green-500"
        default: return "bg-gray-100 border-gray-400"
      }
    },

    // 🔹 SortableJS setup
    initSortable() {
      this.sortables.forEach(s => s.destroy && s.destroy())
      this.sortables = []
      const lists = document.querySelectorAll(".task-list")
      lists.forEach(listEl => {
        const s = new Sortable(listEl, {
          group: "shared",
          animation: 150,
          draggable: ".task-card",
          handle: ".drag-handle",
          ghostClass: "sortable-ghost",
          onEnd: evt => {
            const fromColIndex = Number(evt.from.dataset.colIndex)
            const toColIndex = Number(evt.to.dataset.colIndex)
            if (Number.isNaN(fromColIndex) || Number.isNaN(toColIndex)) return
            const fromCol = this.columns[fromColIndex]
            const toCol = this.columns[toColIndex]
            const [movedTask] = fromCol.tasks.splice(evt.oldIndex, 1)
            toCol.tasks.splice(evt.newIndex, 0, movedTask)
            this.saveToLocalStorage()   // ✅ Save after drag
          }
        })
        this.sortables.push(s)
      })
    }
  }
}
</script>


<style>
.sortable-ghost {
  opacity: 1;
  filter: brightness(0.95);
}
</style>








