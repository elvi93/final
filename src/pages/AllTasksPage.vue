<template>
  <h4>This Page Displays all tasks</h4>

  <div class="container">
    <!-- Loop through the tasks array and render each task in a list item -->
    <ul>
      <li v-for="task in tasks" :key="task.id">
        <h5>{{ task.title }}</h5>
        <h6>{{ task.description.title }}</h6>
        <h6>{{ task.description.timeToBeCompleted }}</h6>
        <ul>
          <li
            v-for="(extraInfo, index) in task.description.extraInfoRequired"
            :key="index"
          >
            {{ extraInfo }}
          </li>
        </ul>
        <h6>{{ task.isCompleted ? "Completed" : "Incomplete" }}</h6>
        <div>
          <button id="plus"
            :class="{ completed: task.isCompleted }"
            :disabled="task.isCompleted"
            @click="markTaskCompleted(task.id)"
          >
            {{ task.isCompleted ? "Completed" : "Mark as Completed" }}
          </button>
          <button id="plus" @click="editTask(task)">Edit Task</button>
          <button id="remove" @click="deleteTask(task.id)">Delete Task</button>
          <button id="remove" v-if="task.isCompleted" @click="markTaskIncomplete(task.id)">Mark as Incomplete</button>
        </div>
        <div v-if="task.id === editingTaskId">
          <h5>Edit Task</h5>
          <input v-model="taskToUpdate.title" placeholder="Title" />
          <input
            v-model="taskToUpdate.description.title"
            placeholder="Description Title"
          />
          <input
            v-model="taskToUpdate.description.timeToBeCompleted"
            placeholder="Time to be Completed"
          />
          <input
            v-for="(info, index) in taskToUpdate.description.extraInfoRequired"
            :key="index"
            v-model="taskToUpdate.description.extraInfoRequired[index]"
            placeholder="Extra Info"
          />
          <button id="plus" @click="saveTaskChanges">Save</button>
          <button id="remove" @click="cancelEdit">Cancel</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { useTaskStore } from "../stores/taskStore";

// Accessing the task store
const taskstore = useTaskStore();
const { tasks, deleteTask, markTaskCompleted, markTaskIncomplete, updateTaskInStore } = taskstore;

// Vue reactive variables for managing task editing
const editingTaskId = ref(null);
const taskToUpdate = ref(null);
let originalTask = ref(null);

// Function to initiate task editing
function editTask(task) {
  editingTaskId.value = task.id;
  taskToUpdate.value = { ...task };
  originalTask.value = { ...task }; // Keep a copy of the original task for comparison
}

// Utility function to compare two task objects
function areTasksEqual(task1, task2) {
  return (
    task1.title === task2.title &&
    task1.description.title === task2.description.title &&
    task1.description.timeToBeCompleted === task2.description.timeToBeCompleted &&
    JSON.stringify(task1.description.extraInfoRequired) === JSON.stringify(task2.description.extraInfoRequired)
  );
}

// Function to save changes to a task
function saveTaskChanges() {
  console.log("Saving changes for task:", taskToUpdate.value);

  // Compare the updated task with the original task
  const isTaskChanged = !areTasksEqual(taskToUpdate.value, originalTask.value);

  // If the task has changed, mark it as incomplete
  if (isTaskChanged) {
    taskToUpdate.value.isCompleted = false;
  }

  updateTaskInStore(taskToUpdate.value); // Update task in the global store
  console.log("Tasks after update:", tasks);
  cancelEdit(); // Exit edit mode
}

// Function to cancel editing
function cancelEdit() {
  editingTaskId.value = null;
  taskToUpdate.value = null;
  originalTask.value = null;
}
</script>

<style scoped>
button {
  display: block;
  margin-bottom: 0.5rem;
}
button.completed:hover {
  background-color: hsla(160, 100%, 37%, 0.5) !important; /* Keep the same background color on hover */
}
</style>
