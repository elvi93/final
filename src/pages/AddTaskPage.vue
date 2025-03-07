<!--
This file defines a Vue.js component for adding a new task to a to-do application.
By building this component, we will achieve a user interface that allows users to input details for a new task and add it to the global state managed by Pinia.js.
-->

<template>
  <div class="container">
    <h1>Add New Task</h1>
    <!-- v-if directive to show success message if taskAdded is true, otherwise show the form -->
    <div v-if="taskAdded">
      <p>Yay! New task created.</p>
      <button @click="startNewTask">Start a New Task</button>
    </div>
    <div v-else>
      <form @submit.prevent="handleSubmit">
        <div>
          <label for="title">Title:</label>
          <input v-model="newTask.title" type="text" id="title" required />
        </div>
        <div>
          <label for="descriptionTitle">Description Title:</label>
          <input
            v-model="newTask.description.title"
            type="text"
            id="descriptionTitle"
            required
          />
        </div>
        <div>
          <label for="timeToBeCompleted">Time to be Completed:</label>
          <input
            v-model="newTask.description.timeToBeCompleted"
            type="text"
            id="timeToBeCompleted"
            required
          />
        </div>
        <div>
          <label for="dueDate">Due Date:</label>
          <input
            v-model="newTask.dueDate"
            type="date"
            id="dueDate"
            required
          />
        </div>
        <div>
          <label for="extraInfo">Extra Info Required:</label>
          <input v-model="newExtraInfo" type="text" id="extraInfo" />
          <button id="plus" type="button" @click="addExtraInfo">Add Info</button>
          <ul>
            <li
              v-for="(info, index) in newTask.description.extraInfoRequired"
              :key="index"
            >
              {{ info }}
              <button id="remove" type="button" @click="removeExtraInfo(index)">
                Remove
              </button>
            </li>
          </ul>
        </div>
        <button id="plus" type="submit">Add Task</button>
      </form>
    </div>
  </div>
</template>

<script setup>
// ------------------------------------------------------------------------
// Import Block
// ------------------------------------------------------------------------

// Importing reactive and ref from Vue for reactivity and references
import { reactive, ref } from "vue";
// Importing the useTaskStore function from taskStore to interact with the task store
import { useTaskStore } from "../stores/taskStore";

// ------------------------------------------------------------------------
// Store Access Block
// ------------------------------------------------------------------------

// Use the task store
const taskStore = useTaskStore();
const { addTask } = taskStore; // Destructure addTask function from the task store

// ------------------------------------------------------------------------
// Reactive Variables Block
// ------------------------------------------------------------------------

// Reactive object for the new task
const newTask = reactive({
  id: Date.now(), // Set initial ID to current timestamp
  title: "", // Title of the new task
  description: {
    title: "", // Detailed description of the new task
    timeToBeCompleted: "", // Time required to complete the new task
    extraInfoRequired: [], // Array for additional information required for the task
  },
  dueDate: "", // Due date for the new task
  isCompleted: false, // Initial completion status of the new task
});

const newExtraInfo = ref(""); // Reference for new extra info input
const taskAdded = ref(false); // Reference for tracking if a task has been added

// ------------------------------------------------------------------------
// Methods Block
// ------------------------------------------------------------------------

// Function to handle form submission
const handleSubmit = () => {
  const taskToAdd = JSON.parse(JSON.stringify(newTask)); // Create a deep copy of the new task to avoid reactivity issues
  taskToAdd.id = Date.now(); // Update ID to ensure uniqueness
  addTask(taskToAdd); // Add the new task to the store
  taskAdded.value = true; // Set taskAdded to true to show the success message
};

/*
  The handleSubmit function handles the form submission process.
  - It creates a deep copy of the newTask object to avoid any reactivity issues.
  - It updates the id of the taskToAdd to ensure it is unique by setting it to the current timestamp.
  - It calls the addTask function from the task store to add the new task to the global state.
  - It sets taskAdded to true to indicate that a task has been successfully added.
  */

// Function to add extra info
const addExtraInfo = () => {
  if (newExtraInfo.value.trim()) {
    // Check if the newExtraInfo value is not empty after trimming
    newTask.description.extraInfoRequired.push(newExtraInfo.value.trim()); // Add the trimmed value to the extraInfoRequired array
    newExtraInfo.value = ""; // Clear the input field
  }
};

/*
  The addExtraInfo function adds an extra information item to the new task's description.
  - It checks if the newExtraInfo input value is not empty after trimming whitespace.
  - If valid, it pushes the trimmed value to the extraInfoRequired array of the new task's description.
  - It then clears the newExtraInfo input field.
  */

// Function to remove extra info
const removeExtraInfo = (index) => {
  newTask.description.extraInfoRequired.splice(index, 1); // Remove the item at the specified index from the extraInfoRequired array
};

/*
  The removeExtraInfo function removes an extra information item from the new task's description.
  - It takes an index as a parameter.
  - It removes the item at the specified index from the extraInfoRequired array using the splice method.
  */

// Function to reset the form
const resetForm = () => {
  newTask.title = ""; // Clear the title field
  newTask.description.title = ""; // Clear the description title field
  newTask.description.timeToBeCompleted = ""; // Clear the time to be completed field
  newTask.dueDate = ""; // Clear the due date field
  newTask.description.extraInfoRequired = []; // Clear the extra info required array
};

/*
  The resetForm function resets all the fields of the new task form to their initial states.
  - It clears the title field by setting newTask.title to an empty string.
  - It clears the description title field by setting newTask.description.title to an empty string.
  - It clears the time to be completed field by setting newTask.description.timeToBeCompleted to an empty string.
  - It clears the due date field by setting newTask.dueDate to an empty string.
  - It clears the extra info required array by setting newTask.description.extraInfoRequired to an empty array.
  */

// Function to start a new task submission
const startNewTask = () => {
  resetForm(); // Reset the form fields
  taskAdded.value = false; // Set taskAdded to false to show the form again
};

/*
  The startNewTask function resets the form and prepares it for a new task submission.
  - It calls the resetForm function to clear all form fields.
  - It sets taskAdded to false to hide the success message and display the form again.
  */
</script>

