<template>
  <div
    v-if="isOpen"
    class="fixed inset-0 z-50 flex items-start justify-center bg-black bg-opacity-50"
    @click="handleBackdropClick"
  >
    
    <div class="bg-customRed rounded-3xl px-8 py-4 shadow-lg flex flex-col items-center w-56 max-w-sm mt-12 sm:mt-20">
     
      <h2 class="text-xl text-white font-roboto font-semibold mb-4">Ordene por:</h2>
      
      
      <div class="bg-white rounded-2xl p-4 shadow-md w-48 flex flex-col space-y-4 mx-1">
       
        <label class="flex items-center space-x-2 ">
          <input type="radio" name="sortOption" value="Número" class="form-radio h-5 w-5 text-customRed border-2 border-customRed rounded-full" />
          <span class="text-lg text-black">Número</span>
        </label>
        <label class="flex items-center space-x-2 ">
          <input type="radio" name="sortOption" value="Nome" class="form-radio h-5 w-5 text-customRed border-2 border-customRed rounded-full" />
          <span class="text-lg text-black">Nome</span>
        </label>      
      </div>

     
      <div class="mt-4">
        <button @click="$emit('close')" class="bg-white text-customRed font-bold px-4 py-2 rounded hover:bg-red-100">
          Aplicar
        </button>
      </div>
    </div>      
  </div>
</template>

<script lang="ts">
export default {
  name: 'ModalComponent',
  props: {
    isOpen: {
      type: Boolean,
      required: true,
    },
  },
  methods: {
    handleBackdropClick(event: MouseEvent) {
      
      if (event.target === event.currentTarget) {
        this.$emit('close'); 
      }
    },
  },
};
</script>
