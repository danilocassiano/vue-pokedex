<template>
  <div
    v-if="isOpen"
    class="fixed inset-0 z-50 flex items-center justify-center bg-black bg-opacity-50"
    @click="handleBackdropClick"
  >
    <div class="bg-customRed rounded-3xl p-4 sm:p-6 shadow-lg flex flex-col items-center w-11/12 max-w-sm mt-12 sm:mt-20">
      <h2 class="text-lg sm:text-xl text-white font-roboto font-semibold mb-4">Filtrar por Tipos:</h2>

      <div class="bg-white rounded-2xl p-2 sm:p-4 shadow-md w-full grid grid-cols-2 sm:grid-cols-3 gap-2 sm:gap-4 max-h-96 overflow-y-auto">
        <div
          class="flex items-center space-x-2"
          v-for="(tipo, index) in tipos"
          :key="index"
        >
          <input
            type="checkbox"
            :value="tipo"
            v-model="selectedTipos"
            class="form-checkbox h-5 w-5 text-customRed border-2 border-customRed rounded"
          />
          <span class="text-sm sm:text-lg text-black">{{ tipo }}</span>
        </div>
      </div>

      <div class="mt-4">
        <button @click="applyFilter" class="bg-white text-customRed font-bold px-4 py-2 rounded hover:bg-red-100">
          Aplicar
        </button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType, ref } from 'vue';

export default defineComponent({
  name: 'ModalComponent',
  props: {
    isOpen: {
      type: Boolean as PropType<boolean>,
      required: true,
    },
  },
  setup(_, { emit }) {
    const tipos = ref<string[]>([
      'grass', 'fire', 'water', 'bug', 'normal',
      'electric', 'ground', 'ice', 'fighting',
      'psychic', 'rock', 'ghost', 'dragon',
      'fairy', 'steel', 'flying', 'poison'
    ]);

    const selectedTipos = ref<string[]>([]);

    const handleBackdropClick = (event: MouseEvent) => {
      if (event.target === event.currentTarget) {
        emit('close');
      }
    };

    const applyFilter = () => {
      emit('applyFilter', selectedTipos.value);
      emit('close');
    };

    return {
      handleBackdropClick,
      tipos,
      selectedTipos,
      applyFilter,
    };
  },
});
</script>
