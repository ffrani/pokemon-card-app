<template>
  <div class="title">
  <img src="./assets/Pokemon-Kort.png" border="0" alt="Pokemon Image">
</div>
  <div class="app">
    <div class="search-bar">
      <div class="search-container-wrapper">
        <div class="search-container">
          <input
            @input="fetchSuggestions"
            @keydown.enter="searchCardsOnEnter"
            class="search-input"
            type="text"
            v-model="searchQuery"
            placeholder="Søk etter kort"
          />
          <button class="search-button" @click="searchCards">
            <i class="search-icon fas fa-search"></i>
          </button>
        </div>
        <ul v-if="showSuggestions" class="suggestions-list">
          <li
            v-for="suggestion in suggestions"
            :key="suggestion.id"
            @click="selectSuggestion(suggestion)"
          >
            {{ suggestion.name }}
          </li>
        </ul>
      </div>
    </div>

    <SearchResults
      :results="searchResults"
      :selected-pokemon="selectedPokemon"
      @add-card="addCard"
    ></SearchResults>

    <CardList
      :cards="cardList"
      @edit-card="editCard"
      @remove-card="removeCard"
    ></CardList>
  </div>
</template>

<script setup>
import { ref, provide, computed } from 'vue';
import SearchResults from './components/SearchResults.vue';
import CardList from './components/CardList.vue';

const searchQuery = ref('');
const searchResults = ref([]);
const cardList = ref([]);
const suggestions = ref([]);
const fullList = ref([]);
const selectedPokemon = ref(null);

const searchCards = async () => {
  if (searchQuery.value.trim() === '') {
    return;
  }

  try {
    const response = await fetch(
      `https://pokeapi.co/api/v2/pokemon/?limit=1000`
    );
    if (!response.ok) {
      throw new Error('Failed to fetch search results');
    }
    const data = await response.json();
    const filteredResults = data.results.filter(pokemon =>
      pokemon.name.toLowerCase().startsWith(searchQuery.value.toLowerCase())
    );
    const pokemonDataPromises = filteredResults.map(pokemon =>
      fetch(pokemon.url).then(res => res.json())
    );
    const pokemonData = await Promise.all(pokemonDataPromises);
    const capitalizedResults = pokemonData.map(pokemon => ({
      id: pokemon.id,
      name: pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1),
      image: pokemon.sprites.front_default,
      height: pokemon.height / 10,
      weight: pokemon.weight / 10,
      abilities: pokemon.abilities.map(ability => ability.ability.name),
    }));

    searchResults.value = capitalizedResults;

    searchQuery.value = '';
  } catch (error) {
    console.error(error);
  }
};

const searchCardsOnEnter = (event) => {
  if (event.key === 'Enter') {
    searchCards();
  }
};

const fetchSuggestions = async () => {
  if (searchQuery.value.length > 0) {
    try {
      const response = await fetch(
        'https://pokeapi.co/api/v2/pokemon/?limit=1000'
      );
      if (!response.ok) {
        throw new Error('Failed to fetch suggestions');
      }
      const data = await response.json();
      fullList.value = data.results;

      const filteredResults = fullList.value.filter(pokemon =>
        pokemon.name.toLowerCase().startsWith(searchQuery.value.toLowerCase())
      );
      suggestions.value = filteredResults;
    } catch (error) {
      console.error(error);
    }
  } else {
    suggestions.value = [];
  }
};

const selectSuggestion = async suggestion => {
  searchQuery.value = suggestion.name;
  suggestions.value = [];

  try {
    const response = await fetch(suggestion.url);
    if (!response.ok) {
      throw new Error('Failed to fetch selected Pokemon');
    }
    const pokemon = await response.json();
    const capitalizedPokemon = {
      id: pokemon.id,
      name: pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1),
      image: pokemon.sprites.front_default,
      height: pokemon.height / 10,
      weight: pokemon.weight / 10,
      abilities: pokemon.abilities.map(ability => ability.ability.name),
    };

    selectedPokemon.value = capitalizedPokemon;
  } catch (error) {
    console.error(error);
  }
};

const addCard = (pokemon) => {
  if (pokemon) {
    const existingCard = cardList.value.find(c => c.id === pokemon.id);
    if (!existingCard) {
      cardList.value.push(pokemon);
      selectedPokemon.value = null;
      searchQuery.value = '';
    }
  }
};


const removeCard = card => {
  const index = cardList.value.findIndex(c => c.id === card.id);
  if (index !== -1) {
    cardList.value.splice(index, 1);
  }
};

const editCard = card => {
  card.isEditing = true;
};

provide('addCard', addCard);
provide('editCard', editCard);
provide('removeCard', removeCard);
provide('selectedPokemon', selectedPokemon);

const showSuggestions = computed(() => {
  return suggestions.value.length > 0 && searchQuery.value.length > 0;
});
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
}

body {
  display: flex;
  justify-content: center;
  align-items: center;
  background-image: linear-gradient(to right, #ffff00, #ffcc00);
}

.title {
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.title img {
  width: 450px;
  height: auto;
  margin-top: 15px;
}

.search-bar {
  position: relative;
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

.search-container {
  display: flex;
  align-items: center;
  background-color: #ffff;
  border-radius: 4px;
  padding: 6px;
}

.search-input {
  flex: 1;
  border: none;
  outline: none;
  padding: 8px;
  font-size: 14px;
  background-color: #ffff;
}

.search-button {
  background-color: rgb(57,107,186);
  color: #fff;
  border: none;
  outline: none;
  padding: 8px 12px;
  border-radius: 4px;
  cursor: pointer;
}

.search-button:hover {
  background-color: #0056b3;
}

.suggestions-list {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background-color: #ffff;
  border: 1px solid #ffff;
  border-top: none;
  border-radius: 0 0 4px 4px;
  padding: 0;
  list-style-type: none;
  max-height: 200px;
  overflow-y: auto;
}

.suggestions-list li {
  padding: 8px;
  cursor: pointer;
}

.suggestions-list li:hover {
  background-color: #f5f5f5;
}
.search-container-wrapper {
  position: relative;
}

.suggestions-list {
  position: absolute;
  top: 100%;
  left: 0;
  z-index: 1;
  width: 100%;
  background-color: #fff;
  border: 1px solid #ccc;
  border-top: none;
  list-style: none;
  padding: 0;
  margin: 0;
  max-height: 200px;
  overflow-y: auto;
  box-sizing: border-box;
}

.suggestions-list li {
  padding: 10px;
  cursor: pointer;
}

.suggestions-list li:hover {
  background-color: #f2f2f2;
}
</style>
