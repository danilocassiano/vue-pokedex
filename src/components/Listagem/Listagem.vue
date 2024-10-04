<template>
  <div class="bg-white p-4 m-4 rounded-2xl">
    <div v-if="loading" class="flex items-center justify-center h-40">
      <p class="text-lg font-semibold text-red-600 animate-pulse">Carregando Pokémon...</p>
    </div>

    <div v-else>
      <div class="flex justify-between mb-4">
        <button @click="showFavorites = !showFavorites" class="px-4 py-2 bg-red-500 text-white rounded-md hover:bg-red-600">
          {{ showFavorites ? 'Ver Todos' : 'Lista de Favoritos' }}
        </button>
      </div>

      <div class="grid grid-cols-3 md:grid-cols-6 gap-4">
        <div
          v-for="(pokemon, index) in paginatedPokemons"
          :key="index"
          class="relative p-4 cursor-pointer bg-white border border-gray-200 rounded-2xl shadow-sm transition duration-300 ease-in-out transform hover:scale-105 hover:border-blue-500"
          @click="openModal(pokemon)"
        >
          <div class="absolute bottom-2 left-2 right-2 top-2 bg-gray-100 rounded-lg -z-10"></div>

          <button class="absolute top-2 left-2 z-10 w-5 h-5 bg-transparent" @click.stop="toggleStar(pokemon)">
            <img :src="isStarred(pokemon) ? starYellowImage : starImage" alt="Star" class="w-full h-full object-contain" />
          </button>

          <p class="absolute top-2 right-2 text-gray-300 font-semibold text-right z-10">#{{ pokemon.id }}</p>
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
        <span class="font-semibold">Página {{ currentPage }} de {{ totalPages }}</span>
        <button @click="nextPage" :disabled="currentPage >= totalPages" class="px-4 py-2 bg-gray-300 rounded-md hover:bg-gray-400 disabled:opacity-50">
          Próxima
        </button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { ref, computed, watch, onMounted } from "vue";
import CardPokemon from '../CardPokemon/CardPokemon.vue';
import starIcon from '../../assets/star.png'; 
import starYellowIcon from '../../assets/star_yellow.png'; 

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
    const allPokemons = ref<Pokemon[]>([]);
    const currentPage = ref<number>(1);
    const itemsPerPage = 24;
    const isModalOpen = ref(false);
    const selectedPokemon = ref<Pokemon | null>(null);
    const loading = ref(true); 
    const starredPokemons = ref<number[]>([]); 
    const showFavorites = ref<boolean>(false); // Estado para controlar a exibição de favoritos

    const fetchPokemons = async () => {
      try {
        const res = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=${itemsPerPage * 10}&offset=0`);
        if (!res.ok) throw new Error(`Erro ao buscar dados: ${res.statusText}`);

        const data = await res.json();   
        const pokemonPromises = data.results.map(async (pokemon: any) => {
          const pokemonDetails = await fetch(pokemon.url);
          const details = await pokemonDetails.json();
          const speciesRes = await fetch(details.species.url);
          const speciesDetails = await speciesRes.json();
          return {
            name: pokemon.name,
            id: details.id,
            types: details.types.map((type: any) => type.type.name),
            height: details.height,
            weight: details.weight,
            description: speciesDetails.flavor_text_entries.find((entry: any) => entry.language.name === 'en')?.flavor_text || '',              
          };
        });

        allPokemons.value = await Promise.all(pokemonPromises);
      } catch (error) {
        console.error("Erro ao buscar os pokémons:", error);
      } finally {
        loading.value = false; 
      }
    };

    const getPokemonImageUrl = (id: number) =>
      `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/${id}.svg`;

    const filteredPokemons = computed(() => {
      const busca = props.busca.trim().toLowerCase();

      const filteredByNameOrId = allPokemons.value.filter((pokemon) => {
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

    const filteredAndStarredPokemons = computed(() => {
      return showFavorites.value
        ? allPokemons.value.filter(pokemon => isStarred(pokemon))
        : filteredPokemons.value;
    });

    const totalPages = computed(() => Math.ceil(filteredAndStarredPokemons.value.length / itemsPerPage)); 

    const paginatedPokemons = computed(() => {
      const startIndex = (currentPage.value - 1) * itemsPerPage;
      return filteredAndStarredPokemons.value.slice(startIndex, startIndex + itemsPerPage);
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

    const isStarred = (pokemon: Pokemon) => {
      return starredPokemons.value.includes(pokemon.id);
    };

    const toggleStar = (pokemon: Pokemon) => {
      if (isStarred(pokemon)) {
        starredPokemons.value = starredPokemons.value.filter(id => id !== pokemon.id); 
      } else {
        starredPokemons.value.push(pokemon.id); 
      }
      localStorage.setItem('starredPokemons', JSON.stringify(starredPokemons.value));
    };

    onMounted(() => {
      fetchPokemons();
      const storedStarredPokemons = localStorage.getItem('starredPokemons');
      if (storedStarredPokemons) {
        starredPokemons.value = JSON.parse(storedStarredPokemons);
      }
    });

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
      loading, 
      starImage: starIcon,
      starYellowImage: starYellowIcon,
      toggleStar,
      isStarred,
      showFavorites, // Expose o estado de favoritos
    };
  },
};
</script>
