<template>
  <b-table :data="weatherCalendar">
    <b-table-column field="date" label="日付" header-class="is-primary">
      <template #default="props">
        <p>{{props.row.date}}[{{props.row.weekday}}]</p>
      </template>
    </b-table-column>

    <b-table-column field="weather" label="予報天気" header-class="is-primary">
      <template #default="props">
        <div>{{props.row.weather}}</div>
        <img :src="props.row.image">
      </template>
    </b-table-column>

    <b-table-column field="tempMin" label="最低気温" header-class="is-info">
      <template #default="props">
        <p v-if="!!props.row.tempMin">{{props.row.tempMin}} ℃</p>
        <p v-else>-</p>
      </template>
    </b-table-column>

    <b-table-column field="tempMax" label="最高気温" header-class="is-danger">
      <template #default="props">
        <p v-if="!!props.row.tempMax">{{props.row.tempMax}} ℃</p>
        <p v-else>-</p>
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
    weeklyTempsMinMax: {
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
      return [...this.weeklyWeathers, ...this.weeklyTempsMinMax].reduce((acc, oneDay) => {
        let weatherItem = {};
        if(oneDay.weatherCode) {
          const weather = this.weatherCodes[oneDay.weatherCode];
          weatherItem = {
            time: oneDay.time,
            date: oneDay.time.toLocaleDateString(),
            weekday: weekdayFormatter.format(oneDay.time),
            weather: weather[3],
            image: `https://www.jma.go.jp/bosai/forecast/img/${weather[0]}`,
          };
        } else if(oneDay.tempMin || oneDay.tempMax) {
          weatherItem = {
            time: oneDay.time,
            date: oneDay.time.toLocaleDateString(),
            tempMin: oneDay.tempMin,
            tempMax: oneDay.tempMax,
          };
        }

        const alreadyIdx = acc.findIndex(item => item.time.toDateString() == oneDay.time.toDateString());
        if(alreadyIdx !== -1) {
          acc.splice(alreadyIdx, 1, Object.assign({}, acc[alreadyIdx], weatherItem));
        } else {
          acc.push(weatherItem);
        }

        return acc;
      }, []);
    }
  },
};
</script>
