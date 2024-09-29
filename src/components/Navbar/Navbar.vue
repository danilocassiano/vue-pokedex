<template>
    <nav class="bg-customRed p-4">
      <div class="max-w-screen-xl flex flex-wrap items-center justify-between mx-auto">
        
        <a href="/" class="flex items-center space-x-3 rtl:space-x-reverse">
          <img src="../../assets/pokeball.svg" class="h-6 md:h-8" alt="PokeLogo" />
          <img src="../../assets/Pokedex.svg" class="h-6 md:h-8" alt="PokeNome" />
        </a>
  
        <!-- Input de Pesquisa (Mobile e Desktop) -->
        <div class="flex items-center space-x-3 w-full md:w-auto my-3 md:my-0">
          <div class="relative flex items-center w-80 md:w-96">
            <img
              src="../../assets/search.svg"
              class="absolute left-4 h-4 md:h-5 text-gray-500"
              alt="search"
            />
            <input
              type="text"
              placeholder="Buscar"
              class="border border-gray-300 rounded-full py-1.5 pl-12 pr-3 w-full font-semibold text-sm text-gray-700 placeholder-gray-600 focus:outline-none focus:ring-2 focus:ring-blue-500"
            />
          </div>
  
          <!-- Botão para abrir o modal -->
          <button
            class="flex items-center justify-center w-10 h-10 text-blue-600 bg-white border rounded-full hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-blue-500"
          >
            <svg
              class="w-5 h-5"
              xmlns="http://www.w3.org/2000/svg"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M12 4v16m8-8H4"
              />
            </svg>
          </button>
        </div>
      </div>  
    </nav>
  </template>
  
  <script lang="ts">
  export default {
    name: 'Navbar',
  }
  
  </script>
