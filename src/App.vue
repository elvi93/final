<!-- 
This file defines a Vue.js component for the header of a to-do application.
It manages user authentication states, displays navigation links conditionally based on the user's login status, and includes functionality to log out users.
-->

<template>
  <header class="up top">
    <div class="wrapper">
      <div class="hello-world">
        <!-- Display a welcome message using the HelloWorld component -->
        <HelloWorld msg="Task Manager" />
      </div>
      <!-- Hamburger menu icon -->
      <div class="menu-toggle" @click="toggleMenu">
        <BarsArrowDownIcon class="bars" />
      </div>
      <!-- Navigation links -->
      <nav class="navbar" :class="{ 'active': isMenuOpen }">
        <ul>
          <template v-if="!isLoggedIn">
            <!-- If the user is not logged in, show these links -->
            <li><RouterLink to="/auth/login">Login</RouterLink></li>
            <li><RouterLink to="/auth/register">Register</RouterLink></li>
          </template>
          <template v-else>
            <!-- If the user is logged in, show these links -->
            <li><RouterLink to="/">Home</RouterLink></li>
            <li><RouterLink to="/about">About</RouterLink></li>
            <li><RouterLink to="/all-tasks">All Tasks</RouterLink></li>
            <li><RouterLink to="/completed-tasks">Completed Tasks</RouterLink></li>
            <li><RouterLink to="/add-task">Add New Task</RouterLink></li>
            <li>
              <!-- Make "Logged in as {{ profile.username }}" clickable and link to a user profile page -->
              <RouterLink :to="`/profile/${profile.username}`">{{ profile.username }}</RouterLink>
            </li>
            <li><button id="remove" class="out" @click="handleSignOut">Sign Out</button></li>
          </template>
        </ul>
      </nav>
    </div>
  </header>

  <!-- RouterView to display the current route's component -->
  <RouterView />
</template>

<script setup>
// ------------------------------------------------------------------------
// Import Block
// ------------------------------------------------------------------------

// Import the HelloWorld component
import HelloWorld from "./components/HelloWorld.vue";
// Import ref, onMounted, and onBeforeMount from Vue
import { ref, onMounted } from "vue";
// Import storeToRefs from Pinia to keep reactivity
import { storeToRefs } from "pinia";
// Import useRouter from vue-router for navigation
import { useRouter } from "vue-router";
// Import useUserStore to access user-related data
import { useUserStore } from "../src/stores/user";
import { BarsArrowDownIcon } from "@heroicons/vue/24/outline"; // Ensure correct path and component

// ------------------------------------------------------------------------
// Variable Definition Block
// ------------------------------------------------------------------------

// Router instance for navigation
const router = useRouter();
// Store user accessed easily here
const userStore = useUserStore();
// Destructure the variable 'user' and 'isLoggedIn' out of the store, keeping their reactivity using storeToRefs
const { user, isLoggedIn, profile } = storeToRefs(userStore);
// Reactive variable for menu visibility
const isMenuOpen = ref(false);

// ------------------------------------------------------------------------
// Function to toggle menu visibility
// ------------------------------------------------------------------------

const toggleMenu = () => {
  isMenuOpen.value = !isMenuOpen.value;
};

// ------------------------------------------------------------------------
// Lifecycle Hook: onMounted
// ------------------------------------------------------------------------

// Using the onMounted lifecycle hook to perform actions when the component is mounted
onMounted(() => {
  try {
    // Fetch the user data from the store
    userStore.fetchUser();
    // Check if the user is stored in localStorage
    if (!user.value) {
      // If no user is found, redirect to login page
      router.push({ path: "/auth/login" });
    } else {
      // If user is found, update the reactive variable and redirect to home
      isUserloggedIn.value = true;
      router.push({ path: "/" });
    }
  } catch (error) {
    console.log(error);
  }
});

// ------------------------------------------------------------------------
// Function to Sign Out User
// ------------------------------------------------------------------------

/**
 * Signs out the user and redirects to the login page.
 */
const handleSignOut = () => {
  // Call the signOut function from the user store
  userStore.signOut();
  // Redirect to login page
  router.push({ path: "/auth/login" });
  // Update the reactive variable to false
  isUserloggedIn.value = false;
};

/*
  The handleSignOut function is used to log out the current user.
  - It calls the signOut function from the user store to clear user data.
  - It redirects the user to the login page.
  */

// ------------------------------------------------------------------------
// Additional Lifecycle Hooks (Placeholder for onBeforeMount, onUpdated)
// ------------------------------------------------------------------------

// Additional lifecycle hooks such as onBeforeMount and onUpdated can be added here if needed.
</script>

<style scoped>
/* Add your scoped styles here */
</style>
