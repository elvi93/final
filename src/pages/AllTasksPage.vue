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

const taskstore = useTaskStore();
const { tasks, deleteTask, markTaskCompleted, updateTaskInStore } = taskstore;

const editingTaskId = ref(null);
const taskToUpdate = ref(null);

function editTask(task) {
  editingTaskId.value = task.id; // wE ARE ASSINING THE id that you receive from the functionParam called 'task' and then we assing the .id of that specific task into the vue-reactive variable using reff calledd editingTaskId
  taskToUpdate.value = { ...task }; // Clone task object to prevent direct mutation
}

// EVERYTIME YOU SEE THE ... LIKE YOU HAVE ON LINE 68, THATS AN INDICATOR THAT WHATEVER IS TO THE RIGHT SIDE OF THE LAST DOT IS SPREADING/CREATING-A-COPY OF THE VALUE THAT THE DOTS ARE CONNECTED TO. 
// SO IN SIMPLER TERMS,THE 3 DOTS OF LINE 68 ARE COPYING THE CONTENTS OF THAT SPECIFIC TASK THAT IS BEING RECEIVED AS A PROP. 

function saveTaskChanges() {
  console.log("Saving changes for task:", taskToUpdate.value);
  updateTaskInStore(taskToUpdate.value); // Update task in global store
  console.log("Tasks after update:", tasks);
  cancelEdit();
}

function cancelEdit() {
  editingTaskId.value = null;
  taskToUpdate.value = null;
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
