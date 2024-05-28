<template>
  <div class="search-component">
    <search-input
      v-model="searchQuery"
      placeholder="Search for a city or state"
    />
    <suggestions-list />
    <Display :suggestions="cities" />
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
      cities: [], // set cities  as array type
      country: "", // set country  as string type
      populationCounts: [], //  set populationCounts  as array type
      isLoading: false,
    };
  },
  methods: {
    fetchCities() {
      if (!this.searchQuery) {
        this.cities = [];
        alert("Please enter a name");
        return;
      }
      this.isLoading = true;
      axios
        .post(
          "https://countriesnow.space/api/v0.1/countries/population/cities",
          {
            city: this.searchQuery,
          }
        )
        .then((response) => {
          if (response.data && response.data.data) {
            this.cities = [
              {
                city: response.data.data.city,
                country: response.data.data.country,
                populationCounts: response.data.data.populationCounts,
              },
            ];
            console.log("Cities data updated:", this.cities);
          } else {
            console.error("No data received");
            alert("No data available for the provided city.");
          }
          this.isLoading = false;
        })
        .catch((error) => {
          console.error("Error fetching city data:", error);
          this.isLoading = false;
        });
    },
  },
  watch: {
    searchQuery() {
      this.fetchCities();
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
