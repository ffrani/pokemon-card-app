<template>
  <div>
    <div class="title-resultater">
    <img src="../assets/resultaterpng.png" alt="Resultater" class="title-image-resultater">
    </div>
    <div class="search-results">
      <ul>
        <li v-for="result in results" :key="result.id">
          <div class="card">
            <h1>{{ result.name }}</h1>
            <img :src="result.image" :alt="result.name" class="card-image" />
            <button @click="onAddCard(result)" class="card-button">
              Legg til
            </button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { inject, defineProps } from 'vue';

defineProps(['results']);

const addCard = inject('addCard');

const onAddCard = result => {
  if (addCard) {
    addCard(result);
  }
};
</script>

<style>

.title-resultater {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.title-image-resultater {
  width: 125px;
  height: 35px;
  margin-right: 5px;
  margin-top: 15px;
}

.title span {
  font-size: 20px;
}

.search-results {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  height: 300px;
  overflow-y: auto;
  border: solid 3px rgb(57,107,186);
  border-radius: 10px;
  min-width: 600px;
}

.search-results .card {
  background-color: #ffff;
  border: 5px solid rgb(57,107,186);
  border-radius: 15px;
  padding: 15px;
  width: 150px;
  margin-top: 10px;

  text-align: center;
}

.search-results h1 {
  font-size: 15px;
}

.search-results .card p {
  font-size: 16px;
  margin-bottom: 5px;
}

.search-results .card .card-image {
  width: 150px;
  height: 150px;
  margin-bottom: 10px;
}

.search-results .card .card-button {
  background-color: #4caf50;
  color: #fff;
  border: none;
  border-radius: 5px;
  padding: 8px 12px;
  margin-bottom: 10px;
  font-size: 14px;
  cursor: pointer;
}

.search-results .card .card-button:hover {
  background-color: #45a049;
}

.search-results ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}
</style>
