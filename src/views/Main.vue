<template>
  <div class="min-h-screen flex flex-col bg-customRed">     
    <Navbar @update:busca="updateBusca" />
    <Listagem :busca="busca" />
    <Rodape />    
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import Navbar from '../components/Navbar/Navbar.vue';
import Listagem from '../components/Listagem/Listagem.vue';
import Rodape from '../components/Rodape/Rodape.vue';

export default defineComponent({
  name: 'Main',
  components: {
    Navbar,
    Rodape,
    Listagem     
  },
  setup() {
    const busca = ref("");

    const updateBusca = (valor: string) => {
      busca.value = valor;
    };

    return {
      busca,
      updateBusca,
    };
  },
});
</script>
