<template>
  <input
    type="text"
    class="search-input"
    :placeholder="placeholder"
    v-model="inputValue"
    @input="$emit('update:searchQuery', inputValue)"
  />
</template>

<script>
export default {
  props: {
    searchQuery: {
      type: String,
      default: "",
    },
    placeholder: {
      type: String,
      default: "City or State",
    },
  },
  data() {
    return {
      inputValue: this.searchQuery,
    };
  },
  watch: {
    searchQuery(newVal) {
      this.inputValue = newVal;
    },
  },
};
</script>

<style>
.search-input {
  width: 100%;
  padding: 10px;
  margin: 10px 0;
  border: 2px solid #ccc;
  border-radius: 4px;
}
</style>
