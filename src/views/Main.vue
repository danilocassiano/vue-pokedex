<template>
  <div class="min-h-screen flex flex-col bg-customRed">
    <Navbar @update:busca="updateBusca" @applyFilter="updateBuscaTipo" />
    <Listagem :busca="busca" :buscaTipo="buscaTipo" />
    <Rodape />
  </div>
</template>

<script lang="ts">
import { ref } from 'vue';
import Navbar from '../components/Navbar/Navbar.vue';
import Listagem from '../components/Listagem/Listagem.vue';
import Rodape from '../components/Rodape/Rodape.vue';

export default {
  components: {
    Navbar,
    Listagem,
    Rodape,
  },
  setup() {
    const busca = ref("");
    const buscaTipo = ref<string[]>([]);

    const updateBusca = (novaBusca: string) => {
      busca.value = novaBusca;
    };

    const updateBuscaTipo = (tipos: string[]) => {
      buscaTipo.value = tipos; 
      console.log("Tipos filtrados:", buscaTipo.value); 
    };

    return {
      busca,
      buscaTipo,
      updateBusca,
      updateBuscaTipo,
    };
  },
};
</script>
