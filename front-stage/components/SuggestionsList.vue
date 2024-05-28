<template>
  <ul class="suggestions-list">
    <li
      v-for="(suggestion, index) in suggestions"
      :key="index"
      @click="selectCity(suggestion)"
    >
      {{ suggestion }}
    </li>
  </ul>
</template>

<script>
import axios from "axios";

export default {
  props: {
    suggestions: Array,
  },
  data() {
    return {
      allCities: [],
    };
  },
  mounted() {
    this.fetchCities();
  },
  methods: {
    fetchCities() {
      axios
        .get("https://countriesnow.space/api/v0.1/countries")
        .then((response) => {
          this.allCities = response.data.data.reduce((acc, country) => {
            acc.push(...country.cities);
            return acc;
          }, []);
        })
        .catch((error) => {
          console.error("Error fetching cities:", error);
        });
    },
    selectCity(city) {
      this.$emit("city-selected", city); // Emitting an event with the city as a payload
    },
  },
};
</script>

<style>
.all-cities {
  padding: 20px;
}
.all-cities h1 {
  text-align: center;
}
.all-cities ul {
  list-style: none;
  padding: 0;
}
.all-cities li {
  padding: 10px;
  border-bottom: 1px solid #ccc;
}
</style>
