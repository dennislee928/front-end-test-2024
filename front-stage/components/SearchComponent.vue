<template>
  <div class="search-component">
    <search-input
      class="sticky-search"
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
    <div v-if="noResultsVisible" class="no-results-overlay">
      <v-card :loading="isLoading" outlined>
        <v-card-title
          >We currently don't have the information of this city.</v-card-title
        >
        <v-card-actions>
          <v-btn color="red" @click="closeNoResults">Close</v-btn>
        </v-card-actions>
      </v-card>
    </div>
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
      noResultsVisible: false,
      majorCities: [
        "Tokyo",
        "Jakarta",
        "Guangzhou",
        "Manila",
        "Shanghai",
        "Seoul",
        "Bangkok",
        "Buenos Aires",
        "Kyoto",
        "Jerusalem",
      ],
    };
  },
  mounted() {
    this.fetchAllCities(); // Fetch all cities when component mounts
  },
  computed: {
    filteredCities() {
      if (!this.searchQuery) {
        return this.majorCities;
      }
      const query = this.searchQuery.toLowerCase();
      let result = this.cities
        .filter((cityName) => cityName.toLowerCase().includes(query))
        .sort()
        .slice(0, 15);
      this.noResultsVisible =
        result.length === 0 && this.searchQuery.length > 0; // Automatically manage no results overlay visibility
      return result;
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
    closeNoResults() {
      this.noResultsVisible = false; // Manually handle closing of the no results overlay
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
  width: 90%; /* Default width for mobile devices */
  margin: auto;
  padding: 20px;
  position: relative; /* Ensures the sticky positioning context is set */
}

/* Media query for non-mobile screens */
@media (min-width: 600px) {
  .search-component {
    max-width: 600px; /* Maximum width for non-mobile devices */
  }
}

.sticky-search {
  position: -webkit-sticky; /* For Safari */
  position: sticky;
  top: 0; /* Set the sticky top offset */
  background: white; /* Ensure the background is not transparent */
  z-index: 10; /* Make sure it's above other content */
  padding: 10px 0; /* Optional: Add padding to maintain layout */
}

/* Additional styles for other elements */
.suggestions-list {
  padding-top: 20px; /* Add space between the search input and the list */
}

.no-results-overlay {
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

.no-results-overlay p {
  background: white;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}
</style>
