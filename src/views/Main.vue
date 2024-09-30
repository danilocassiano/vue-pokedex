<template>
  <div class="min-h-screen flex flex-col bg-customRed">     
    <Navbar />
    <Listagem />
    <Rodape />    
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import Navbar from '../components/Navbar/Navbar.vue';
import Listagem from '../components/Listagem/Listagem.vue';
import Rodape from '../components/Rodape/Rodape.vue';

export default defineComponent({
  name: 'Main',
  components: {
    Navbar,
    Listagem,
    Rodape,    
}
});

</script>
