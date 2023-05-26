<template>
  <div class="auto-suggestions">
    <ul v-if="showSuggestions">
      <li
        v-for="suggestion in suggestions"
        :key="suggestion.id"
        @click="selectSuggestion(suggestion)"
      >
        {{ suggestion.name }}
      </li>
    </ul>
  </div>
</template>

<script>
import { inject, defineProps } from 'vue';

export default {
  props: {
    suggestions: {
      type: Array,
      required: true,
    },
  },
  setup() {
    const addCard = inject('addCard');
    const selectSuggestion = suggestion => {
      if (addCard) {
        addCard(suggestion);
      }
    };

    return {
      selectSuggestion,
    };
  },
};
</script>

<style scoped>
.auto-suggestions {
  position: relative;
}

ul {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100%;
  list-style: none;
  background-color: #fff;
  padding: 0;
  margin: 0;
  border: 1px solid #ccc;
}

li {
  padding: 5px;
  cursor: pointer;
}

li:hover {
  background-color: #f5f5f5;
}
</style>
