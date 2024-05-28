<!-- SuggestionsList.vue -->
<template>
  <ul v-if="suggestions.length > 0" class="suggestions-list">
    <li v-for="(suggestion, index) in suggestions" :key="index">
      {{ suggestion }}
    </li>
  </ul>
  <p v-else>No matching cities found</p>
</template>

<script>
export default {
  props: {
    suggestions: {
      type: Array,
      default: () => [],
    },
  },
};
</script>

<style>
/* Add your styles here */
</style>
