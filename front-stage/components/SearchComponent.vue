<template>
  <div class="search-component">
    <search-input
      v-model="searchQuery"
      placeholder="Search for a city or state"
    />
    <suggestions-list
      :suggestions="filteredCities"
      @city-selected="fetchCityData"
    />

    <CityOverlay
      :cityData="cityData"
      :visible="overlayVisible"
      @close="closeOverlay"
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
      const query = this.searchQuery.toLowerCase();
      return this.cities.filter((cityName) =>
        cityName.toLowerCase().includes(query)
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
            // Only add city names, verify they are indeed strings
            const cityNames = country.cities.filter(
              (name) => typeof name === "string"
            );
            return acc.concat(cityNames);
          }, []);
          console.log("All cities fetched (post-process):", this.cities);
          this.isLoading = false;
        })
        .catch((error) => {
          console.error("Error fetching all cities:", error);
          this.isLoading = false;
        });
    }, //search single city
    fetchCityData(cityName) {
      if (typeof cityName !== "string") {
        console.error("Expected city name as a string, received:", cityName);
        return;
      }
      this.isLoading = true;
      axios
        .post(
          "https://countriesnow.space/api/v0.1/countries/population/cities",
          { city: cityName }
        )
        .then((response) => {
          if (response.data && response.data.data) {
            this.cityData = response.data.data;
            this.overlayVisible = true;
            console.log("Fetched data for city:", cityName);
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
    //close the overlay
    closeOverlay() {
      console.log("Closing overlay");
      this.overlayVisible = false;
    },
  },
  watch: {
    searchQuery(newVal, oldVal) {
      //  if (newVal && newVal !== oldVal) {
      //  this.fetchCityData();
      //console.log("fetched!"); // Fetch data whenever searchQuery changes
      //}
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
