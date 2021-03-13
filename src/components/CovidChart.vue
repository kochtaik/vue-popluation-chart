<template>
  <section class="chart-container">
    <h1
      v-if="isEmptyTimeline"
    >Oops! There's no data for choosen country:(</h1>
    <canvas
      id="covidChart"
      width="400"
      height="400"
      aria-label="Dynamic of covid-19" role="img"
    ></canvas>
  </section>
  <general-stats
    v-if="isChartDrawn"
    :localitiesDatasets="localitiesDatasets"
  ></general-stats>
</template>
<script>
import GeneralStats from './GeneralStats.vue';

import Chart from 'chart.js';
import { fetchData } from '../api';
export default {
  name: 'CovidChart',
  components: {
    GeneralStats,
  },
  data() {
    return {
      constantCountryTimeline: [],
      localitiesDatasets: [],
      chartInstance: null,
      isChartDrawn: false,
      isEmptyTimeline: false,
    };
  },

  props: {
    choosenCountry: {
      type: Object,
      required: false,
      default: null,
    },
    timespan: {
      type: Number,
      required: true,
      default: Infinity,
    }
  },

  emits: ['on-error'],

  methods: {
    async prepareData(countryCode = '') {
      try {
        const localityData = (countryCode === '') ? await fetchData('', '/timeline')
          : await fetchData(countryCode);

        const { data } = localityData;
        if (!this.isValidTimeline(data.timeline)) return;
        let latestData;

        // in case of global timeline
        if (!data.name) {
          latestData = data[0];
          this.configureLatestData(latestData, 'Global');
        } else {
          latestData = data.timeline[0];
          const { name, code } = this.choosenCountry;
          this.configureLatestData(latestData, name, code);
          this.constantCountryTimeline = data.timeline;
          this.drawChart();
        }
        this.renewLocalitiesDatasets(latestData);
      } catch (e) {
        console.error(e);
        this.$emit('on-error');
      }
    },

    configureLatestData(data, name, countryCode) {
      if (countryCode) {
        data.code = countryCode
      }
      data.name = name;
      return data;
    },

    isValidTimeline(timeline) {
      this.isEmptyTimeline = false;
      if (timeline?.length === 0) {
        this.isEmptyTimeline = true;
        this.isChartDrawn = false;
        this.chartInstance.destroy();
        return false;
      }
      return true;
    },

    renewLocalitiesDatasets(dataset) {
      const allowableDatasetsLength = 2;
      if (this.localitiesDatasets.length === allowableDatasetsLength) {
        this.localitiesDatasets.pop();
      }
      this.localitiesDatasets.push(dataset);
    },

    selectDataByKey(key) {
      return this.currentCountryTimeline.reduceRight((dataset, item) => {
        let handledValue = item[key];
        if (key === 'date') {
          const options = { year: 'numeric', month: 'short', day: 'numeric'};
          handledValue = new Date(handledValue).toLocaleString('us-US', options);
        }
        dataset.push(handledValue);
        return dataset;
      }, []);
    },

    drawChart() {
      const dates = this.selectDataByKey('date');
      const newConfirmedCases = this.selectDataByKey('new_confirmed');
      const newDeaths = this.selectDataByKey('new_deaths');
      const ctx = document.getElementById('covidChart');

      this.isChartDrawn = false;
      if (this.chartInstance !== null) this.chartInstance.destroy();
      const config = {
        type: 'bar',
        data: {
          labels: dates,
          datasets: [{
            type: 'line',
            label: 'New deaths',
            backgroundColor: '#d95d5a',
            borderColor: '#c5221f',
            borderWidth: 1,
            pointRadius: 1,
            data: newDeaths,
          },
          {
            label: 'New cases',
            backgroundColor: '#aecbfa',
            borderColor: '#aecbfa',
            data: newConfirmedCases,
            fill: false,
          }]
        },
        options: {
          responsive: true,
          title: {
            display: true,
            text: this.choosenCountry.name,
            fontSize: 20,
          },
          hover: {
            mode: 'nearest',
            intersect: false
          },
          scales: {
            yAxes: [{
              display: true,
              ticks: {
                beginAtZero: true
              }
            }],
            xAxes: [{
              display: true,
              ticks: {
                callback: function(dataLabel, idx) {
                  if (idx === dates.length - 1) return dataLabel;
                  return idx % 10 === 0 ? dataLabel : '';
                }
              }
            }]
          },
          tooltips: {
            mode: 'index',
            intersect: false
          },
        }
      };
      this.chartInstance = new Chart(ctx, config);
      this.isChartDrawn = true;
    },
  },
  computed: {
    currentCountryTimeline() {
      return this.constantCountryTimeline.slice(0, this.timespan);
    }
  },
  watch: {
    choosenCountry() {
      const countryCode = `/${this.choosenCountry.code}`;
      this.prepareData(countryCode);
    },

    timespan() {
      this.drawChart();
    },
  },
  mounted() {
    this.prepareData();
  }
}
</script>

<style scoped>
  @import url('../assets/styles/CovidChartStyles.css');
</style>