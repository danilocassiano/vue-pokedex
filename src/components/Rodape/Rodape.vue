<template>
    <footer class="flex justify-end bg-customRed px-6 py-3">
      <span class="text-white text-sm">Todos os Direitos Reservados &copy; {{ currentYear }}</span>
    </footer>
  </template>
  
  <script>
  export default {
    computed: {
      currentYear() {
        return new Date().getFullYear();
      }
    }
  }
  </script>
