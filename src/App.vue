<template>
  <config-bar
    @search-input="makeSuggestions"
    @country-select="setCountry"
    @change-interval="setTimespan"
    :suggestions="suggestions"
    ></config-bar>
  <div class="stats-container">
    <covid-chart
      :choosenCountry="choosenCountry"
      :timespan="timespan"
    ></covid-chart>
  </div>
  <div class="overlay" v-if="showSpinner">
    <base-spinner></base-spinner>
  </div>
</template>

<script>
import configBar from './components/configBar.vue';
import CovidChart from './components/CovidChart.vue';
import BaseSpinner from './components/BaseSpinner.vue';
import { fetchData } from './api';

export default {
  name: 'Covid dashboard',

  components: {
    configBar,
    CovidChart,
    BaseSpinner
  },

  data() {
    return {
      countriesList: [],
      suggestions: [],
      countryTimeline: [],
      choosenCountry: null,
      timespan: Infinity,
      loaded: false,
    };
  },

  methods: {
    async formCountriesList() {
      const countries = await fetchData();
      this.countriesList = this.extractCountriesNames(countries.data);
      const firstCountry = this.countriesList[0];
      this.setCountry(firstCountry);
    },

    makeSuggestions(inputValue) { 
      if (inputValue.length > 0) {
      const toMatch = new RegExp(`^${inputValue}`, 'gi');
      this.suggestions = this.countriesList
        .filter(countryInfo => toMatch.test(countryInfo.name))
        .sort()
        .slice(0, 4);
      } else this.suggestions = [];
    },

    extractCountriesNames(countriesInfo) {
      return countriesInfo.reduce((countriesNames, country) => {
        const countryNameInfo = {
          name: country.name,
          code: country.code
        };
        countriesNames.push(countryNameInfo);
        return countriesNames;
      }, []);
    },

    setCountry(countryName) {
      this.choosenCountry = countryName;
      this.suggestions = [];
    },

    setTimespan(interval) {
      this.timespan = Number(interval);
    }

  },

  beforeCreate() {
    this.showSpinner = true;
  },

  mounted() {
    this.formCountriesList();
    this.showSpinner = false;
  },
}
</script>

<style>
html, select, input {
  font-size: 1em;
  font-family: 'Roboto', sans-serif;
  font-weight: 300;
}

.stats-container {
  position: relative;
  display: flex;
  align-items: flex-start;
  justify-content: space-around;
  flex-wrap: wrap;
}

.overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: none;
  background: rgba(0, 0, 0, 0.363);
  display: flex;
  align-items: center;
  justify-content: center;
}

@media (min-width: 1550px) and (max-width: 1920px) {
  html {
    font-size: 2.2em;
  }
}
@media (min-width: 1280px) and (max-width: 1550px) {
  html {
    font-size: 1.5em;
  }
}

@media (min-width: 600px) and (max-width: 960px) {
  html {
    font-size: 1.5em;
  }
  .stats-container {
    flex-direction: column;
    align-items: center;
  }
}
@media (min-width: 320px) and (max-width: 600px) {
  html {
    font-size: 0.9em;
  }
}
</style>
