<template>
  <ul class="suggestions-list">
    <li class="cities" v-for="(cityName, index) in suggestions" :key="index">
      {{ cityName }}
      <v-btn class="button" variant="tonal" @click="selectCity(cityName)"
        >Show this city</v-btn
      >
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
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 50;
}
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
.cities {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #ccc;
  margin: 10px;
}
</style>
