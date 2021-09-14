<template>
  <b-table :data="weatherCalendar">
    <b-table-column field="date" label="日付">
      <template #default="props">
        {{props.row.date}}[{{props.row.weekday}}]
      </template>
    </b-table-column>

    <b-table-column field="weather" label="予報天気">
      <template #default="props">
        <div>{{props.row.weather}}</div>
        <img :src="props.row.image">
      </template>
    </b-table-column>
  </b-table>
</template>

<script>
export default {
  name: 'WeeklyWeather',
  props: {
    weeklyWeathers: {
      type: Array,
      default: () => {
        return [];
      }
    },
    weatherCodes: {
      type: Object,
      default: () => {
        return {};
      }
    }
  },
  computed: {
    weatherCalendar: function () {
      const weekdayFormatter = new Intl.DateTimeFormat(navigator.language, { weekday: 'short' });
      return this.weeklyWeathers.map(oneDayWeather => {
        const weather = this.weatherCodes[oneDayWeather.weatherCode];
        return {
          date: oneDayWeather.time.toLocaleDateString(),
          weekday: weekdayFormatter.format(oneDayWeather.time),
          weather: weather[3],
          image: `https://www.jma.go.jp/bosai/forecast/img/${weather[0]}`,
        };
      });
    }
  },
};
</script>
