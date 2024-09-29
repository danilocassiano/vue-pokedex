<template>
    <div class="min-h-screen flex flex-col bg-customRed">     
      <Navbar />    
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent } from 'vue';
  import Navbar from "../components/Navbar/Navbar.vue" 
  export default defineComponent({
    name: 'Main',
    components: {
      Navbar,     
  }
});

  </script>
 