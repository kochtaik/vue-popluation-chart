<template>
  <div class="locality">
    <div class="locality__name">
      <img
        :src="linkToFlagIcon"
        :alt="alternativeCaption"
      />
      {{ localityStats.name }}
    </div>
    <div class="locality__total-cases cell">
      <span class="cell__title">All cases</span>
      <span class="cell__number-value">{{ localityStats.confirmed || 'N/A' }}</span>
      <span class="cell__increase">{{ localityStats.new_confirmed || 'N/A' }}</span>
    </div>
    <div class="locality__total-recovered cell">
      <span class="cell__title">Recovered</span>
      <span class="cell__number-value">{{ localityStats.recovered || 'N/A' }}</span>
      <span class="cell__increase">{{ localityStats.new_recovered || 'N/A' }}</span>
    </div>
    <div class="locality__total-deaths cell">
      <span class="cell__title">Deaths</span>
      <span class="cell__number-value">{{ localityStats.deaths || 'N/A' }}</span>
      <span class="cell__increase">{{ localityStats.new_deaths || 'N/A' }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'BaseLocality',
  props: {
    localityStats: {
      type: Object,
      required: true,
      default: null,
    }
  },
  computed: {
    linkToFlagIcon() {
      // if localityStats contains global stats
      if (!this.localityStats.code) {
        return 'https://img.icons8.com/fluent-systems-regular/32/4a90e2/globe--v1.png';
      }
      const localityCode = this.localityStats.code.toLowerCase();
      return `https://www.countryflags.io/${localityCode}/flat/32.png`;
    },
    alternativeCaption() {
      if (this.localityStats.name === 'Global') {
        return 'An icon of globe';
      } return `A flag of ${this.localityStats.name}`;
    }
  },
}
</script>

<style scoped>
  @import url('../assets/styles/BaseLocalityStyles.css');
</style>