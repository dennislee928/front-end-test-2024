<template>
  <ul class="suggestions-list">
    <li v-for="(cityName, index) in suggestions" :key="index">
      {{ cityName }}
      <button @click="selectCity(cityName)">Show this city</button>
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
      allCities: [], // Maybe redundant if you already receive cities as suggestions
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
            return acc.concat(country.cities); // Assume country.cities are strings
          }, []);
          this.$emit("update-suggestions", this.allCities); // Emit an event to update suggestions in the parent
        })
        .catch((error) => {
          console.error("Error fetching cities:", error);
        });
    },
    selectCity(cityName) {
      this.$emit("city-selected", cityName); // Emit the city name directly
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
