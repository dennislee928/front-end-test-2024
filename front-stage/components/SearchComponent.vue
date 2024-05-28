<template>
  <div class="search-component">
    <search-input
      v-model="searchQuery"
      placeholder="Search for a city or state"
    />
    <suggestions-list
      v-if="filteredCities.length > 0"
      :suggestions="filteredCities"
    />
  </div>
</template>

<script>
import axios from "axios";
import SearchInput from "./SearchInput.vue";
import SuggestionsList from "./SuggestionsList.vue";

export default {
  components: {
    SearchInput,
    SuggestionsList,
  },
  data() {
    return {
      searchQuery: "",
      cities: [], // This will store all cities fetched from the API
      isLoading: false,
    };
  },
  mounted() {
    this.fetchAllCities(); // Fetch all cities when component mounts
  },
  computed: {
    filteredCities() {
      // Filter cities based on the search query
      return this.cities.filter((city) =>
        city.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
  },
  methods: {
    fetchAllCities() {
      this.isLoading = true;
      axios
        .get("https://countriesnow.space/api/v0.1/countries")
        .then((response) => {
          this.cities = response.data.data.reduce((acc, country) => {
            return acc.concat(country.cities); // Flatten the city names into a single array
          }, []);
          this.isLoading = false;
        })
        .catch((error) => {
          console.error("Error fetching all cities:", error);
          this.isLoading = false;
        });
    },
  },
  watch: {
    searchQuery(newVal, oldVal) {
      // Optionally, you could fetch specific city data here if needed
    },
  },
};
</script>
<style>
.search-component {
  max-width: 600px;
  margin: auto;
  padding: 20px;
}
</style>
