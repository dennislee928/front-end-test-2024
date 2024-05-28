<template>
  <div class="search-component">
    <search-input
      v-model="searchQuery"
      placeholder="Search for a city or state"
    />
    <suggestions-list
      v-if="filteredCities.length > 0"
      :suggestions="filteredCities"
      @city-selected="updateSearchQuery"
    />
    <city-overlay
      :cityData="cityData"
      :visible="overlayVisible"
      @close="overlayVisible = false"
    />
  </div>
</template>

<script>
import axios from "axios";
import SearchInput from "./SearchInput.vue";
import SuggestionsList from "./SuggestionsList.vue";
import CityOverlay from "./CityOverlay.vue";

export default {
  components: {
    SearchInput,
    SuggestionsList,
    CityOverlay,
  },
  data() {
    return {
      searchQuery: "",
      cities: [], // This will store all cities fetched from the API
      isLoading: false,
      cityData: {},
      overlayVisible: false,
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
    //search single city
    fetchCityData() {
      if (!this.searchQuery) {
        alert("Please enter a city name");
        return;
      }
      this.isLoading = true;
      axios
        .post(
          "https://countriesnow.space/api/v0.1/countries/population/cities",
          { city: this.searchQuery }
        )
        .then((response) => {
          if (response.data && response.data.data) {
            // Assuming the API returns data as shown in your example
            this.cities = [response.data.data]; // Wrapping in an array to match expected format
            console.log("Fetched data for city:", this.cities);
            this.overlayVisible = true;
            console.log("overlaystatus:" + this.overlayVisible);
          } else {
            console.error("No data received");
            alert("No data available for the provided city.");
          }
          this.isLoading = false;
        })
        .catch((error) => {
          console.error("Error fetching data for city:", error);
          this.isLoading = false;
        });
    },

    //
    updateSearchQuery(city) {
      this.searchQuery = city;
      console.log("city=" + city);
      // Update searchQuery when a city is selected from the list
    },
  },
  watch: {
    searchQuery(newVal, oldVal) {
      if (newVal && newVal !== oldVal) {
        this.fetchCityData();
        console.log("fetched!"); // Fetch data whenever searchQuery changes
      }
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
