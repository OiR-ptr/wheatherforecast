<template>
  <div id="app">
    <weather-forecast 
      :weather-codes="weatherCodes" 
      :office-codes="officeCodes" 
      :center-codes="centerCodes" />
  </div>
</template>

<script>
import WeatherForecast from './components/WeatherForecast.vue'

export default {
  name: 'App',
  data: () => {
    return {
      weatherCodes: {},
      centerCodes: {},
      officeCodes: {},
    };
  },
  components: {
    WeatherForecast,
  },
  created() {
    this.initializeTelops();
    this.initializeOffices();
  },
  methods: {
    initializeTelops() {
      let self = this;
      fetch(`TELOPS.json`).then(data => {
        return data.json();
      }).then(telops => {
        self.$set(self, 'weatherCodes', telops);
      }).catch(error => console.error(error))
    },
    initializeOffices() {
      let self = this;
      fetch(`https://www.jma.go.jp/bosai/common/const/area.json`).then(data => {
        return data.json();
      }).then(area => {
        self.$set(self, 'officeCodes', area?.offices);
        self.$set(self, 'centerCodes', area?.centers);
      }).catch(error => console.error(error));
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
