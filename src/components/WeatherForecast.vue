<template>
  <div>
    <h1>Weather Forecast</h1>
    <p>{{this.weatherCodes}}</p>
  </div>
</template>

<script>
export default {
  name: 'WeatherForecast',
  props: {
    weatherCodes: {
      type: Object,
      default: () => {
        return { item: 'baka' };
      }
    },
  },
  updated: {
    weatherCodes() {
      debugger;
      console.log(this.weatherCodes);
    }
  }
}
</script>
