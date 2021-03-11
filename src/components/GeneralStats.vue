<template>
  <section>
    <header>General statistics</header>
    <base-locality
      v-for="(localityDataset, idx) in handledData"
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
      default: null,
    }
  },
  computed: {
    handledData() {
      return this.localitiesDatasets.map(dataset => {
          return Object.entries(dataset).reduce((acc, [key, value]) => {
          // ignoring dates
          const dateValidation = /([12]\d{3}-(0[1-9]|1[0-2])-(0[1-9]|[12]\d|3[01]))/gm;
          if (dateValidation.test(value)) return acc;
    
          acc[key] = value.toLocaleString();
          if (key.startsWith('new')) {
            acc[key] = `+${acc[key]}`;
          }
          return acc;
        }, {});
      });
    }
  }
}
</script>

<style scoped>
  section {
    width: auto;
    height: 50%;
    border: 1px solid #cecece;
    border-radius: 7px;
  }

  header {
    padding: 1em;
    font-weight: 500;
    border-bottom: 1px solid #cecece;
  }
</style>