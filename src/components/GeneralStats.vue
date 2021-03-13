<template>
  <section>
    <header>General statistics</header>
    <base-locality
      v-for="(localityDataset, idx) in formattedData"
      :key="idx"
      :localityStats="localityDataset"
    ></base-locality>
  </section>
</template>

<script>

import BaseLocality from './BaseLocality.vue';

export default {
  name: 'GeneralStats',

  components: {
    BaseLocality,
  },

  props: {
    localitiesDatasets: {
      type: Object,
      required: true,
      default: null,
    }
  },
  computed: {
    // Prepares data before outputing
    // by transforming numbers into human-readable format.
    formattedData() {
      return this.localitiesDatasets.map(dataset => {
          return Object.entries(dataset).reduce((formattedObj, [key, value]) => {
    
          formattedObj[key] = value.toLocaleString();
          if (key.startsWith('new')) {
            formattedObj[key] = `+${formattedObj[key]}`;
          }
          return formattedObj;
        }, {});
      });
    }
  }
}
</script>

<style scoped>
  section {
    width: 40%;
    height: 50%;
    border: 1px solid #cecece;
    border-radius: 7px;
    box-shadow: 0 10px 10px rgba(0, 0, 0, 0.2);
    margin: 0.5em;
  }

  header {
    padding: 1em;
    font-weight: 500;
    border-bottom: 1px solid #cecece;
  }

  @media (max-width: 960px) {
    section {
      width: 80%;
    }
  }
</style>