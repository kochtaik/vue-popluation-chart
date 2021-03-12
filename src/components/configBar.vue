<template>
  <nav>
    <div>
      <div class="search-area">
        <input
          class="country-input"
          v-model="searchStringValue"
          @keypress.enter="selectCountry"
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
  props: ['suggestions'],
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
      this.$emit('country-select', selectedCountry);
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
  }
}
</script>

<style scoped>
  nav {
    background: #00adb5;
    height: 15vh;
    display: flex;
    align-items: center;
    margin-bottom: 1em;
  }

  nav div {
    display: flex;
    align-items: center;
    justify-content: flex-start;
  }

  select, .country-input {
    margin: 0 0.5em;
  }

  select {
    display: none;
  }

  .country-input {
    background: inherit;
    border: 0;
    outline: 0;
    color: #eeeeee;
  }

  .country-input::placeholder {
    color: #eeeeee;
  }

  .search-area {
    position: relative;
  }

  ul {
    width: 92%;
    padding: 0;
    margin: 0 0.5em;
    list-style: none;
    position: absolute;
    top: 100%;
    z-index: 1000;
    background: rgb(255, 255, 255);
    box-shadow:
    0 2.8px 2.2px rgba(0, 0, 0, 0.034),
    0 6.7px 5.3px rgba(0, 0, 0, 0.048),
    0 12.5px 10px rgba(0, 0, 0, 0.06),
    0 22.3px 17.9px rgba(0, 0, 0, 0.072),
    0 41.8px 33.4px rgba(0, 0, 0, 0.086),
    0 100px 80px rgba(0, 0, 0, 0.12)
  ;
  }

  li {
    margin: 0.5em 0;
    padding: 0 0.5em;
    cursor: pointer;
  }

  li:hover {
    background: #efefef;
  }

  form {
    display: flex;
    align-items: center;
  }

  .radio {
    margin: 0.5rem;
    display: flex;
    flex-direction: column;
  }

  .radio input[type="radio"] {
    opacity: 0;
    position: absolute;
  }

  .radio label {
    color: #eeeeee;
    cursor: pointer;
    padding: 0.1em;
    border-radius: 5px;
    transition: all 0.25s;
  }

  .radio input[type="radio"]:checked + label {
   background-color: #222831;
  }

  @media (max-width: 960px) {
    section {
      width: 80%;
    }

    select {
      display: block;
      font-size: 1.2em;
    }

    form {
      display: none;
    }
  }
</style>
