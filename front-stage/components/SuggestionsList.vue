<template>
  <div class="all-cities">
    <ul>
      <li v-for="(city, index) in allCities" :key="index">{{ city }}</li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";

export default {
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
