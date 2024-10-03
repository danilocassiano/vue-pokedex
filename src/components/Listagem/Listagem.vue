<template>
  <div class="bg-white p-4 m-4 rounded-2xl">
    <div class="grid grid-cols-3 md:grid-cols-6 gap-4">
      <div
        v-for="(pokemon, index) in paginatedPokemons"
        :key="index"
        class="relative p-4 cursor-pointer bg-white border border-gray-200 rounded-2xl shadow-sm transition duration-300 ease-in-out transform hover:scale-105 hover:border-blue-500"
        @click="openModal(pokemon)"
      >
        <div class="absolute bottom-2 left-2 right-2 top-2 bg-gray-100 rounded-lg -z-10"></div>
        <p class="text-gray-300 font-semibold text-right z-10">#{{ pokemon.id }}</p>
        <img :src="getPokemonImageUrl(pokemon.id)" alt="Pokemon Image" class="h-20 w-20 mx-auto z-10" />
        <h3 class="font-roboto text-center font-semibold text-[0.8125rem] z-10">{{ pokemon.name }}</h3>
      </div>
    </div>

    <CardPokemon
      v-if="isModalOpen && selectedPokemon"
      :isOpen="isModalOpen"
      :pokemon="selectedPokemon"
      @close="closeCard"
    />

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

<script lang="ts">
import { ref, computed, watch, onMounted } from "vue";
import CardPokemon from '../CardPokemon/CardPokemon.vue';

interface Pokemon {
  name: string;
  id: number;
  types: string[];
  height: number;     
  weight: number;
  description: string; 
}

export default {
  name: "Listagem",
  components: {
    CardPokemon,
  },
  props: {
    busca: {
      type: String,
      default: ""
    },
    buscaTipo: {
      type: Array as () => string[],
      default: () => []
    }
  },
  setup(props) {
    const pokemons = ref<Pokemon[]>([]);
    const currentPage = ref(1);
    const itemsPerPage = 24;
    const isModalOpen = ref(false);
    const selectedPokemon = ref<Pokemon | null>(null);

    const fetchPokemons = async () => {
      try {
        const res = await fetch("https://pokeapi.co/api/v2/pokemon?limit=150");
        if (!res.ok) throw new Error(`Erro ao buscar dados: ${res.statusText}`);

        const data = await res.json();   
             

        pokemons.value = await Promise.all(
          data.results.map(async (pokemon: any, index: number) => {
            const pokemonDetails = await fetch(pokemon.url);
            const details = await pokemonDetails.json();
            const speciesRes = await fetch(details.species.url);
            const speciesDetails = await speciesRes.json();
            return {
              name: pokemon.name,
              id: index + 1,
              types: details.types.map((type: any) => type.type.name),
              height: details.height,
              weight: details.weight,
              description: speciesDetails.flavor_text_entries.find((entry: any) => entry.language.name === 'en')?.flavor_text || '',              
            };
          })
        );
        
      } catch (error) {
        console.error("Erro ao buscar os pokémons:", error);
      }
    };

    const getPokemonImageUrl = (id: number) =>
      `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/${id}.svg`;

    const filteredPokemons = computed(() => {
      const busca = props.busca.trim().toLowerCase();

      const filteredByNameOrId = pokemons.value.filter((pokemon) => {
        const buscaPorId = parseInt(busca);
        return !isNaN(buscaPorId)
          ? pokemon.id === buscaPorId
          : pokemon.name.toLowerCase().includes(busca);
      });

      if (props.buscaTipo.length > 0) {
        return filteredByNameOrId.filter((pokemon) =>
          pokemon.types.some((type) => props.buscaTipo.includes(type))
        );
      }

      return filteredByNameOrId;
    });

    const totalPages = computed(() => Math.ceil(filteredPokemons.value.length / itemsPerPage));

    const paginatedPokemons = computed(() => {
      const startIndex = (currentPage.value - 1) * itemsPerPage;
      return filteredPokemons.value.slice(startIndex, startIndex + itemsPerPage);
    });

    
    watch(() => props.busca, () => {
      currentPage.value = 1; 
    });

    const openModal = (pokemon: Pokemon) => {
      selectedPokemon.value = pokemon;
      isModalOpen.value = true; 
    };

    const closeCard = () => {      
      isModalOpen.value = false; 
      selectedPokemon.value = null; 
    };

    const nextPage = () => {
      if (currentPage.value < totalPages.value) {
        currentPage.value++;
      }
    };

    const prevPage = () => {
      if (currentPage.value > 1) {
        currentPage.value--;
      }
    };

    onMounted(fetchPokemons);

    return {
      currentPage,
      paginatedPokemons,
      totalPages,
      nextPage,
      prevPage,
      getPokemonImageUrl,
      isModalOpen,
      selectedPokemon,
      openModal,
      closeCard,
    };
  },
};
</script>

<style scoped>
.font-roboto {
  font-family: 'Roboto', sans-serif;
}
</style>
