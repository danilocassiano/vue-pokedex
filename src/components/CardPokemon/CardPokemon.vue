<template>
  <div v-if="isOpen" class="fixed inset-0 z-50 flex items-center justify-center bg-black bg-opacity-50" @click.self="close">
    <div
      :class="`relative rounded-lg shadow-lg p-4 w-72 transition duration-300 ease-in-out`"
      :style="{ backgroundColor: getTypeColor(pokemon.types[0]) }"
      @click.stop
    >
      <img src="../../assets/card.svg" alt="Card Image" class="absolute inset-0 w-full h-full object-cover opacity-20" />

      <div class="relative z-10">
        <div class="flex items-center mb-2">
          <button @click="close" class="mr-2 cursor-pointer focus:outline-none">
            <img src="../../assets/Vector.svg" alt="Close" class="w-4 h-4" />
          </button>
          <h2 class="text-lg font-bold text-white capitalize flex-grow">
            {{ pokemon.name }}
          </h2>
          <span class="text-white text-base font-bold ml-auto">
            #{{ pokemon.id.toString().padStart(3, '0') }}
          </span>
        </div>

        <div class="flex items-center justify-center z-0 mb-2">
          <img
            :src="getPokemonImageUrl(pokemon.id)"
            alt="Pokemon Image"
            class="h-36 w-36 mx-auto"
          />
        </div>

        <div class="mt-2 bg-white p-1 rounded-lg shadow">
          <div class="flex justify-center space-x-1 mt-1">
            <span
              v-for="(type, index) in pokemon.types"
              :key="index"
              :style="{ backgroundColor: getTypeColor(type) }"
              class="px-1 py-0.5 rounded-full text-xs text-white"
            >
              {{ type }}
            </span>
          </div>

          <h3 class="text-base font-semibold mt-2 text-center" :style="{ color: getTypeColor(pokemon.types[0]) }">Sobre</h3>
          <div class="flex justify-center items-center mt-1 space-x-2 text-gray-700">
            <div class="text-center">
              <span class="text-xs font-bold">Altura:</span>
              <p class="text-sm">{{ (pokemon.height / 10).toFixed(1) }} m</p>
            </div>
            <div class="text-center">
              <span class="text-xs font-bold">Peso:</span>
              <p class="text-sm">{{ (pokemon.weight / 10).toFixed(1) }} kg</p>
            </div>
          </div>

          <h3 class="text-base font-semibold mt-2 mb-1 text-center" :style="{ color: getTypeColor(pokemon.types[0]) }">Estatísticas</h3>
          <div class="grid grid-rows-3 mt-2 text-gray-700">
            <div
              v-for="(stat, index) in stats"
              :key="index"
              class="col-span-3 flex items-center justify-between mb-1 leading-tight"
            >
              <span class="text-xs font-semibold mr-2 w-20" :style="{ color: getTypeColor(pokemon.types[0]) }">
                {{ getFormattedStatName(stat.stat.name) }}
              </span>
              <span class="mr-2 w-8 text-right text-xs">{{ stat.base_stat }}</span>
              <div class="flex-1 bg-gray-200 rounded-full h-1.5 overflow-hidden">
                <div
                  class="h-1.5 rounded-full"
                  :style="{ backgroundColor: getTypeColor(pokemon.types[0]), width: Math.min(stat.base_stat, 100) + '%' }"
                ></div>
              </div>
            </div>
          </div>

          <button @click="showEvolutionChain = !showEvolutionChain" 
          class="mt-2 w-full text-white py-1 rounded"
          :style="{ background: getTypeColor(pokemon.types[0]) }">
            {{ showEvolutionChain ? 'Esconder Cadeia Evolutiva' : 'Mostrar Cadeia Evolutiva' }}
          </button>

          <div v-if="showEvolutionChain" class="mt-2">
            <h3 class="text-base font-semibold text-center" :style="{ color: getTypeColor(pokemon.types[0]) }">Cadeia de Evolução</h3>
            <div class="flex justify-center space-x-2 mt-2">
              <div v-if="evolutionChain.length" class="flex space-x-2">
                <div v-for="(evolution, index) in evolutionChain" :key="index" class="text-center">
                  <img :src="getPokemonImageUrl(evolution.id)" alt="Evolution Image" class="h-12 w-12" />
                  <p class="text-xs">{{ evolution.name }}</p>
                </div>
              </div>
              <p v-else class="text-gray-500 text-xs">Sem evoluções.</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType, ref, onMounted } from 'vue';

interface PokemonStat {
  base_stat: number;
  stat: {
    name: string;
  };
}

interface EvolutionChain {
  species: {
    name: string;
    url: string;
  };
  evolves_to: EvolutionChain[];
}

interface Evolution {
  name: string;
  id: number;
}

const typeColors: Record<string, string> = {
  fire: "#F08030",
  water: "#6890F0",
  grass: "#78C850",
  electric: "#F8D030",
  ice: "#98D8D8",
  fighting: "#C03028",
  poison: "#A040A0",
  ground: "#E0C068",
  flying: "#A890F0",
  psychic: "#F85888",
  bug: "#A8B820",
  rock: "#B8A038",
  ghost: "#705898",
  dragon: "#7038F8",
  dark: "#705848",
  steel: "#B8B8D0",
  fairy: "#EE99AC",
};

export default defineComponent({
  name: 'CardPokemon',
  props: {
    isOpen: {
      type: Boolean,
      required: true,
    },
    pokemon: {
      type: Object as PropType<{
        name: string;
        id: number;
        types: string[];
        description: string;
        height: number;
        weight: number;
      }>,
      required: true,
    },
  },
  setup(props, { emit }) {
    const stats = ref<PokemonStat[]>([]);
    const evolutionChain = ref<Evolution[]>([]);
    const showEvolutionChain = ref(false); 

    const getPokemonStats = async () => {
      const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${props.pokemon.id}`);
      const data = await response.json();
      stats.value = data.stats;
    };

    const fetchEvolutionChain = async () => {
      const speciesResponse = await fetch(`https://pokeapi.co/api/v2/pokemon-species/${props.pokemon.id}`);
      const speciesData = await speciesResponse.json();
      const evolutionResponse = await fetch(speciesData.evolution_chain.url);
      const evolutionData = await evolutionResponse.json();

      const extractEvolutions = (chain: EvolutionChain): Evolution[] => {
        const evolutions: Evolution[] = [{
          name: chain.species.name,
          id: parseInt(chain.species.url.split('/').slice(-2, -1)[0]), 
        }];

        chain.evolves_to.forEach(evolve => {
          evolutions.push(...extractEvolutions(evolve));
        });

        return evolutions;
      };

      evolutionChain.value = extractEvolutions(evolutionData.chain);
    };

    const getFormattedStatName = (statName: string): string => {
      const statMapping: Record<string, string> = {
        hp: 'HP',
        attack: 'ATK',
        defense: 'DEF',
        'special-attack': 'SATK',
        'special-defense': 'SDEF',
        speed: 'SPD',
      };
      return statMapping[statName] || statName;
    };

    onMounted(() => {
      getPokemonStats();
      fetchEvolutionChain();
      document.addEventListener('keydown', handleKeyDown);
    });

    const getPokemonImageUrl = (id: number) => {
      return `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/${id}.svg`;
    };

    const getTypeColor = (type: string) => {
      return typeColors[type] || "#000000"; 
    };

    const close = () => {
      emit('close');
    };

    const handleKeyDown = (event: KeyboardEvent) => {
      if (event.key === 'Escape') {
        close();
      }
    };

    return {
      stats,
      evolutionChain,
      showEvolutionChain,
      getFormattedStatName,
      close,
      getPokemonImageUrl,
      getTypeColor,
      handleKeyDown,
    };
  },
});
</script>