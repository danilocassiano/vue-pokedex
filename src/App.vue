<template>
  <Main /> 
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Main from '../src/views/Main.vue';

export default defineComponent({
  name: 'App',
  components: {
    Main,
  },
});
</script>
