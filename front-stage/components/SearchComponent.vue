<template>
  <div class="search-component">
    <search-input
      v-model="searchQuery"
      placeholder="Search for a city or state"
    />
    <suggestions-list :suggestions="cities" />
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
      cities: [],
      isLoading: false,
    };
  },
  methods: {
    fetchCities() {
      if (!this.searchQuery) {
        this.cities = []; // 清空cities array
        alert("please enter a name");
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
          // 假設有程式資料回傳
          this.cities = [response.data.data]; // Adjust according to actual response structure
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
      // This watcher will call fetchCities whenever searchQuery changes
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
