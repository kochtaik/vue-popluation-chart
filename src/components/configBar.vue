<template>
  <nav>
    <div>
      <div class="search-area">
        <input
          class="country-input"
          v-model="searchStringValue"
          @keydown.enter="selectCountry"
          @keydown.esc="closeSuggestionsList"
          type="text"
          placeholder="Enter a country name..."
          />
        <ul
          class="suggestions"
          v-if="suggestions.length"
          >
          <li
            v-for="(countryInfo, idx) in suggestions"
            @click="selectCountry($event)"
            :key="idx"
            tabindex="0"
          >{{ countryInfo.name }}</li>
        </ul>
      </div>
      <select v-model="interval" id="timing">
        <option value="Infinity">All time</option>
        <option value="7">1 week</option>
        <option value="14">2 weeks</option>
        <option value="30">30 days</option>
      </select>
    </div>
    <form action="">
      <div class="radio">
        <input
          v-model="interval"
          name="timespan"
          value="Infinity"
          id="all-time" 
          type="radio"
        ><label for="all-time">All time</label>
      </div>
      <div class="radio">
        <input
          v-model="interval"
          name="timespan"
          value="7"
          id="week1" 
          type="radio"
        ><label for="week1">1 week</label>
      </div>
      <div class="radio">
        <input 
          v-model="interval"
          name="timespan"
          value="14"
          id="weeks2"
          type="radio"
        ><label for="weeks2">2 weeks</label>
      </div>
      <div class="radio">
        <input
          v-model="interval"
          name="timespan" 
          value="30"
          id="month"
          type="radio"
          ><label for="month">30 days</label>
      </div>
    </form>
  </nav>
</template>

<script>

export default {
  name: 'configBar',
  emits: [
    'search-input',
    'country-select',
    'change-interval',
  ],
  props: {
    suggestions: {
      type: Object,
      default: null,
    }
  },
  data() {
    return {
      interval: 'Infinity',
      searchStringValue: '',
    };
  },
  methods: {
    selectCountry(option) {
      const selectedCountry = this.suggestions.find(country => country.name === option.target.textContent)
        || this.suggestions[0];
      if (!selectedCountry) return;
      this.$emit('country-select', selectedCountry);
      this.closeSuggestionsList();
    },
    closeSuggestionsList() {
      this.searchStringValue = '';
    }
  },
  watch: {
    searchStringValue() {
      this.$emit('search-input', this.searchStringValue);
    },
    interval() {
      this.$emit('change-interval', this.interval);
    },
  },
  mounted() {
    window.addEventListener('click', this.closeSuggestionsList);
  }
}
</script>

<style scoped>
  @import url('../assets/styles/ConfigBarStyle.css');
</style>
