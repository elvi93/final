<template>
  <!-- Header for the tasks page -->
  <h4>This Page Displays all tasks</h4>

  <div class="container">
    <!-- Loop through the tasks array and render each task in a list item -->
    <ul>
      <li v-for="task in tasks" :key="task.id">
        <!-- Task details -->
        <h5>{{ task.title }}</h5>
        <h6>{{ task.description.title }}</h6>
        <h6>{{ task.description.timeToBeCompleted }}</h6>
        <!-- Display due date if it exists -->
        <h6 v-if="task.dueDate">Due Date: {{ task.dueDate }}</h6>
        <!-- List of extra info required -->
        <ul>
          <li
            v-for="(extraInfo, index) in task.description.extraInfoRequired"
            :key="index"
          >
            {{ extraInfo }}
          </li>
        </ul>
        <!-- Display task completion status -->
        <h6>{{ task.isCompleted ? "Completed" : "Incomplete" }}</h6>
        <!-- Button actions for task management -->
        <div>
          <button
            id="plus"
            :class="{ completed: task.isCompleted }"
            :disabled="task.isCompleted"
            @click="markTaskCompleted(task.id)"
          >
            {{ task.isCompleted ? "Completed" : "Mark as Completed" }}
          </button>
          <button id="plus" @click="editTask(task)">Edit Task</button>
          <button id="remove" @click="deleteTask(task.id)">Delete Task</button>
          <!-- Option to mark completed tasks as incomplete -->
          <button
            id="remove"
            v-if="task.isCompleted"
            @click="markTaskIncomplete(task.id)"
          >
            Mark as Incomplete
          </button>
        </div>
        <!-- Edit task section -->
        <div v-if="task.id === editingTaskId">
          <!-- Edit task form -->
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
          <!-- Input field for setting due date -->
          <input
            type="date"
            v-model="taskToUpdate.dueDate"
            placeholder="Due Date"
          />
          <!-- Loop through extra info required -->
          <input
            v-for="(info, index) in taskToUpdate.description.extraInfoRequired"
            :key="index"
            v-model="taskToUpdate.description.extraInfoRequired[index]"
            placeholder="Extra Info"
          />
          <!-- Save and cancel buttons -->
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

// Access the task store
const taskstore = useTaskStore();
// Destructure necessary functions and data from the task store
const { tasks, deleteTask, markTaskCompleted, markTaskIncomplete, updateTaskInStore } = taskstore;

// Define reactive variables for task editing
const editingTaskId = ref(null); // Holds the ID of the task being edited
const taskToUpdate = ref(null); // Holds the task object being updated
let originalTask = ref(null); // Holds the original task object before editing

// Function to initiate task editing
function editTask(task) {
  editingTaskId.value = task.id; // Set the editingTaskId to the ID of the task being edited
  taskToUpdate.value = { ...task }; // Create a copy of the task to update
  originalTask.value = { ...task }; // Create a copy of the original task before editing
}

// Function to check if two tasks are equal
function areTasksEqual(task1, task2) {
  return (
    task1.title === task2.title &&
    task1.description.title === task2.description.title &&
    task1.description.timeToBeCompleted === task2.description.timeToBeCompleted &&
    JSON.stringify(task1.description.extraInfoRequired) === JSON.stringify(task2.description.extraInfoRequired) &&
    task1.dueDate === task2.dueDate // Compare due dates
  );
}

// Function to save changes made to a task
function saveTaskChanges() {
  console.log("Saving changes for task:", taskToUpdate.value);

  const isTaskChanged = !areTasksEqual(taskToUpdate.value, originalTask.value); // Check if there are any changes

  if (isTaskChanged) {
    taskToUpdate.value.isCompleted = false; // Set isCompleted to false if changes were made
  }

  updateTaskInStore(taskToUpdate.value); // Update the task in the store
  console.log("Tasks after update:", tasks); // Log updated tasks to console
  cancelEdit(); // Cancel editing mode after saving
}

// Function to cancel editing a task
function cancelEdit() {
  editingTaskId.value = null; // Reset editingTaskId to null
  taskToUpdate.value = null; // Reset taskToUpdate to null
  originalTask.value = null; // Reset originalTask to null
}
</script>

<style scoped>
button {
  display: block;
  margin-bottom: 0.5rem;
}

button.completed:hover {
  background-color: hsla(160, 100%, 37%, 0.5) !important;
}

.container ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}

.container li {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  padding: 1rem;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
}

@media (max-width: 768px) {
  .container ul {
    grid-template-columns: 1fr;
  }
}

.container h5,
.container h6 {
  margin: 0.5rem 0;
}

.container button {
  margin-top: 0.5rem;
}

.container .extra-info {
  margin-top: 0.5rem;
}
</style>
