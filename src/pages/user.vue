<template>
    <div class="container">
      <h1>User Profile</h1>
      <div class="profile-info">
        <label>Username:</label>
        <span v-if="!isEditing">{{ profile.username }}</span>
        <input v-else v-model="editedUsername" @keydown.enter="saveUsername" />
        <button @click="toggleEditMode">
          {{ isEditing ? 'Save' : 'Edit' }}
        </button>
      </div>
    </div>
  </template>
  
  <script setup>
  // ------------------------------------------------------------------------
  // Import Block
  // ------------------------------------------------------------------------
  
  // Import ref from Vue for reactive references
  import { ref } from 'vue';
  // Import useUserStore to access user-related data
  import { useUserStore } from '../stores/user';
  
  // ------------------------------------------------------------------------
  // Variable Definition Block
  // ------------------------------------------------------------------------
  
  // Access user store
  const { profile, fetchUser, updateUsername } = useUserStore();
  
  // Reactive reference for edited username
  const editedUsername = ref(profile.value?.username);
  
  // Reactive reference to track edit mode
  const isEditing = ref(false);
  
  // Fetch user and profile data on component mount
  fetchUser();
  
  // ------------------------------------------------------------------------
  // Function to toggle edit mode
  // ------------------------------------------------------------------------
  
  /**
   * Toggles the edit mode for username.
   */
  const toggleEditMode = () => {
    isEditing.value = !isEditing.value;
    if (!isEditing.value) {
      // Exit edit mode, save the username
      saveUsername();
    }
  };
  
  // ------------------------------------------------------------------------
  // Function to save username
  // ------------------------------------------------------------------------
  
  /**
   * Saves the edited username.
   */
  const saveUsername = async () => {
    await updateUsername(editedUsername.value);
    toggleEditMode();
  };
  </script>
  
  <style scoped>
  /* Scoped styles for the profile component */
  
  
  .profile-info {
    margin-top: 20px;
  }
  
  label {
    font-weight: bold;
  }
  
  span {
    margin-left: 10px;
  }
  
  input {
    margin-left: 10px;
    padding: 5px;
  }
  
  button {
    margin-left: 10px;
  }
  </style>
  