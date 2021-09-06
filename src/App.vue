<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
    <weather-forecast :weather-codes="weatherCodes" />
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import WeatherForecast from './components/WeatherForecast.vue'

export default {
  name: 'App',
  data: () => {
    return {
      weatherCodes: {},
      officeCodes: {},
    };
  },
  components: {
    HelloWorld,
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
        self.$set(self, 'officeCodes', area);
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
