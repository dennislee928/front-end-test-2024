<template>
  <ul v-if="suggestions.length > 0" class="suggestions-list">
    <li v-for="(suggestion, index) in suggestions" :key="index">
      <span class="name"
        >{{ suggestion.city }}, {{ suggestion.state }},{{
          suggestion.data.country
        }}</span
      >
      <span class="population">{{
        suggestion.population | numberWithCommas
      }}</span>
    </li>
  </ul>
  <p v-else class="no-results">No results found</p>
</template>

<script>
export default {
  props: {
    suggestions: {
      type: Array,
      default: () => [],
    },
  },
  filters: {
    numberWithCommas(x) {
      return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
  },
};
</script>

<style>
.suggestions-list {
  list-style: none;
  padding: 0;
  margin: 0;
}
.suggestions-list li {
  padding: 10px;
  border-bottom: 1px solid #eee;
  display: flex;
  justify-content: space-between;
}
.no-results {
  text-align: center;
  padding: 20px;
  color: #666;
}
</style>
