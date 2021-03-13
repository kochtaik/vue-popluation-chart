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
      @on-error="showErrorMessage"
    ></covid-chart>
  </div>
  <base-overlay v-if="isError">
    <p>
      It seems that your internet connection has gone:(
      Fix it up and then reload the page.
    </p>
  </base-overlay>
</template>

<script>
import configBar from './components/configBar.vue';
import CovidChart from './components/CovidChart.vue';
import BaseOverlay from './components/BaseOverlay.vue';
import { fetchData } from './api';

export default {
  name: 'Covid dashboard',

  components: {
    configBar,
    CovidChart,
    BaseOverlay,
  },

  data() {
    return {
      countriesList: [],
      suggestions: [],
      countryTimeline: [],
      choosenCountry: null,
      timespan: Infinity,
      isError: false,
    };
  },

  methods: {
    async formCountriesList() {
      try {
        const countries = await fetchData();
        this.countriesList = this.extractCountriesNames(countries.data);
        const firstCountry = this.countriesList[0];
        this.setCountry(firstCountry);
      } catch(e) {
        console.error(e);
        this.isError = true;
      }
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
    },

    showErrorMessage() {
      this.isError = true;
      document.body.style.overflowY = 'hidden';
    }

  },

  mounted() {
    this.formCountriesList();
  },
}
</script>

<style>
  @import url('./assets/styles/AppStyles.css');
</style>
