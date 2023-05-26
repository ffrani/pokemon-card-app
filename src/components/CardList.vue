<template>
  <div class="card-list">
    <div class="title-pokedex">
    <img src="../assets/Dine-Pokedex.png" alt="Pokedex" class="title-image-pokedex">
    </div>
    <div class="card-list-container">
      <ul>
        <li v-for="card in cards" :key="card.id" class="card-item">
          <h1>{{ card.name }}</h1>
          <img :src="card.image" :alt="card.name" />
          <p>Ferdigheter: {{ card.abilities.join(', ') }}</p>
          <p>Høyde: {{ card.height }}m</p>
          <p>Vekt: {{ card.weight }}kg</p>
          <button @click="editCard(card)" class="button-rediger">Rediger</button>
          <button @click="handleRemoveCard(card)" class="button-fjern">Fjern</button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { inject, defineProps } from 'vue';

defineProps(['cards']);

const editCard = inject('editCard');
const removeCard = inject('removeCard');

const handleRemoveCard = card => {
  if (removeCard) {
    removeCard(card);
  }
};
</script>

<style>

.title-image-pokedex {
  width: 150px;
  height: 35px;
  margin-right: 5px;
  margin-top: 20px;
}

.card-list-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
  height: 360px; 
  overflow-y: auto;
  border: solid 3px rgb(57,107,186);
  border-radius: 10px;
  min-width: 750px;
}

.card-item {
  background-color: #ffff;
  border: 5px solid rgb(57,107,186);
  border-radius: 15px;
  padding-top: 15px;
  width: 200px;
  margin: 10px;
  text-align: center;
}

.card-item h1 {
  font-size: 15px;
}

.card-item img {
  width: 100px;
  height: 100px;
  margin-bottom: 10px;
}

.card-item p {
  font-size: 14px;
  margin-bottom: 5px;
}

.card-item .abilities {
  font-weight: bold;
}

.button-rediger {
  background-color: rgb(57,107,186);
  color: #fff;
  border: none;
  border-radius: 5px;
  padding: 8px 12px;
  font-size: 13px;
  cursor: pointer;
  margin-right: 5px;
  margin-top: 10px;
  margin-bottom: 10px;
}

.button-rediger:hover {
  background-color: rgb(21, 71, 145);
}

.button-fjern {
  background-color: red;
  color: #fff;
  border: none;
  border-radius: 5px;
  padding: 8px 12px;
  font-size: 13px;
  cursor: pointer;
  margin-right: 5px;
  margin-top: 10px;
  margin-bottom: 10px;
}

.button-fjern:hover {
  background-color: rgb(226, 0, 0);
}

.card-item .buttons {
  margin-top: 10px;
}

.card-item button-rediger:hover {
  background-color: #45a049;
}

.card-list-container ul {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 20px;
  list-style-type: none;
  padding: 0;
  margin: 0;
}
</style>
