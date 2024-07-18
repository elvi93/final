<!--
This file defines a Vue.js component for the login process in a to-do application.
By building this component, we will achieve a user interface that allows users to log in by providing their email and password, with state management handled by Pinia.js.
-->

<template>
  <div class="container">
    <h3 class="header-title">Log In to ToDo Apppppp</h3>
    <BeakerIcon class="icon-test" />
   
    <!-- FORM GOES HERE -->
    <form @submit.prevent="signIn">
      <div class="form">
        <!-- Email Input -->
        <label>E-mail
          <div class="input-wrapper">
            <EnvelopeIcon class="icon-inside" />
            <input id="email" type="text" v-model="formState.email" />
          </div>
        </label>
        <!-- Password Input -->
        <label>Password
          <div class="input-wrapper">
            <EyeSlashIcon class="icon-inside" />
            <input id="password" type="password" v-model="formState.password" />
          </div>
        </label>
        <!-- Button -->
        <!-- I personally like semantic elements, I think they are easier to read as an engineer -->
        <button type="submit">Log In</button>
      </div>
    </form>
    <!-- END FORM -->
    <!-- Error Message Here -->
    <p class="error" v-show="formState.errorMsg">{{ formState.errorMsg }}</p>
    <!-- END Error Message -->

    <p>
      Don't have an account?
      <PersonalRouter
        :route="route"
        :buttonText="buttonText"
        class="sign-up-link"
      />
    </p>
  </div>
</template>

<script setup>
// ------------------------------------------------------------------------
// Import Block
// ------------------------------------------------------------------------

import { reactive } from "vue";
import { useRouter } from "vue-router";
import PersonalRouter from "./PersonalRouter.vue";
import { useUserStore } from "../stores/user";
import { storeToRefs } from "pinia";
import { faUserSecret } from '@fortawesome/free-solid-svg-icons'
import { EnvelopeIcon, EyeSlashIcon } from '@heroicons/vue/24/outline'
// ------------------------------------------------------------------------
// Variables Block
// ------------------------------------------------------------------------

// Route Variables for navigating users
const route = "/auth/register";
const buttonText = "Sign Up";

// Reactive variable to store email, password, and error messages
const formState = reactive({
  email: "", // Stores the email input
  password: "", // Stores the password input
  errorMsg: "", // Stores any error messages
});

// Router instance for navigation
const router = useRouter();
// Store user accessed easily here
const userStore = useUserStore();
// Destructure the variable 'isLoggedIn' out of the store, keeping its reactivity using the storeToRefs method from Pinia
const { isLoggedIn } = storeToRefs(userStore);

// ------------------------------------------------------------------------
// Functions Block
// ------------------------------------------------------------------------

/**
 * Function to handle the SignIn process.
 * It validates the user's credentials using the signIn function from the user store.
 * On success, it redirects the user to the home page.
 * On failure, it displays an error message.
 */
let signIn = () => {
  try {
    // 1- Hitting the user store, and hitting a function that is used to LOG IN
    userStore.signIn(formState.email, formState.password);
    // 2- Change boolean isLoggedIn variable from the store
    isLoggedIn.value = true;
    // 3 - ReRoute the user to the home page
    router.push({ path: "/" });
  } catch (error) {
    // On failure, let's display an error message
    formState.errorMsg = error.message;
    // Hide this error message after a specific time
    // Hide after 8 seconds, remember that is using always milliseconds
    setTimeout(() => {
      formState.errorMsg = "";
    }, 8000);
  }
};

/*
  The signIn function handles the login process.
  - It validates the user's credentials using the signIn function from the user store.
  - On success, it sets isLoggedIn to true and redirects the user to the home page.
  - On failure, it displays an error message and hides it after 8 seconds.
  */
</script>

<style scoped>
label,
input {
  display: block;
}

.error{
  color: red;
}
.input-wrapper {
  position: relative;
  display: flex;
  align-items: center;
}

.icon-inside {
  position: absolute;
  left: 8px;
  width: 24px;
  height: 24px;
  color: rgb(195, 204, 172);
}

input {
  padding-left: 32px; /* Adjust the padding to make space for the icon */
}

button {
  margin: 0.5rem 0;
}

.icon-test {
  width: 24px;
  height: 24px;
  color: rgb(195, 204, 172);
}
</style>
