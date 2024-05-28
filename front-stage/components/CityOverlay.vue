<template>
  <div class="overlay" v-if="visible">
    <div class="overlay-content">
      <v-card :loading="isLoading" outlined>
        <v-card-title>{{ cityData.city }}</v-card-title>
        <v-card-subtitle>{{ cityData.country }}</v-card-subtitle>
        <v-card-text>
          <div class="clarify">Population info</div>
          <div v-for="pop in cityData.populationCounts" :key="pop.year">
            {{ pop.year }}: {{ pop.value | numberWithCommas }} ({{ pop.sex }})
          </div>
        </v-card-text>
        <!-- Adding v-card-actions for the close button -->
        <v-card-actions>
          <v-btn color="red" @click="close">Close</v-btn>
        </v-card-actions>
      </v-card>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    cityData: {
      type: Object,
      default: () => ({}),
    },
    visible: Boolean,
  },
  filters: {
    numberWithCommas(x) {
      if (!x) return x;
      return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
  },
  methods: {
    close() {
      this.$emit("close");
    },
  },
  watch: {
    visible(newVal) {
      console.log("Overlay visibility changed to:", newVal); // This should log true when expected
    },
  },
};
</script>

<style scoped>
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

.overlay-content {
  background: white;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

button {
  margin-top: 20px;
}

.overlay {
  border-width: 5px;
  border-style: solid;
}
.clarify {
  text-decoration: underline;
}
</style>
