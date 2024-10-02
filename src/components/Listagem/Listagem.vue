<template>
  <div class="bg-white p-4 m-4 rounded-2xl">
    <div class="grid grid-cols-3 md:grid-cols-6 gap-4">
      <div
        v-for="(pokemon, index) in paginatedPokemons"
        :key="index"
        class="relative p-4 cursor-pointer bg-white border border-gray-200 rounded-2xl shadow-sm transition duration-300 ease-in-out transform hover:scale-105 hover:border-blue-500"
      >
        <div class="absolute bottom-2 left-2 right-2 top-2 bg-gray-100 rounded-lg -z-10"></div>
        <p class="text-gray-300 font-semibold text-right z-10">#{{ pokemon.id }}</p>
        <img :src="getPokemonImageUrl(pokemon.id)" alt="Pokemon Image" class="h-20 w-20 mx-auto z-10" />
        <h3 class="font-roboto text-center font-semibold text-[0.8125rem] z-10">{{ pokemon.name }}</h3>
      </div>
    </div>

    <div class="flex justify-between items-center mt-4">
      <button @click="prevPage" :disabled="currentPage === 1" class="px-4 py-2 bg-gray-300 rounded-md hover:bg-gray-400 disabled:opacity-50">
        Anterior
      </button>
      <span class="font-semibold">Página {{ currentPage }}</span>
      <button @click="nextPage" :disabled="currentPage >= totalPages" class="px-4 py-2 bg-gray-300 rounded-md hover:bg-gray-400 disabled:opacity-50">
        Próxima
      </button>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted } from "vue";

export default {
  name: "Listagem",
  props: {
    busca: {
      type: String,
      default: ""
    }
  },
  setup(props) {
    const pokemons = ref([]);
    const currentPage = ref(1);
    const itemsPerPage = 24;

    const fetchPokemons = async () => {
      try {
        const res = await fetch("https://pokeapi.co/api/v2/pokemon?limit=1302");
        const data = await res.json();
        pokemons.value = data.results.map((pokemon, index) => ({ ...pokemon, id: index + 1 }));
      } catch (error) {
        console.error("Erro ao buscar os pokémons:", error);
      }
    };

    const getPokemonImageUrl = (id) =>
      `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/${id}.svg`;

    const totalPages = computed(() => Math.ceil(pokemons.value.length / itemsPerPage));

    
    const filteredPokemons = computed(() => {
      return pokemons.value.filter((pokemon) =>
        pokemon.name.toLowerCase().includes(props.busca.toLowerCase())
      );
    });

    const paginatedPokemons = computed(() => {
      const startIndex = (currentPage.value - 1) * itemsPerPage;
      return filteredPokemons.value.slice(startIndex, startIndex + itemsPerPage);
    });

    const nextPage = () => {
      if (currentPage.value < totalPages.value) currentPage.value++;
    };

    const prevPage = () => {
      if (currentPage.value > 1) currentPage.value--;
    };

    onMounted(fetchPokemons);

    return {
      currentPage,
      paginatedPokemons,
      filteredPokemons,
      totalPages,
      nextPage,
      prevPage,
      getPokemonImageUrl,
    };
  },
};
</script>
