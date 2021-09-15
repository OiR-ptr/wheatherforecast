<template>
  <section>
    <b-field grouped>
      <b-field label="地方" label-position="on-border">
        <b-select v-model="selectedOffice" placeholder="Country">
            <optgroup v-for="center in centers" :key="center.key" :label="center.name">
              <option v-for="prefecture in getPrefectures(center.key)" :key="prefecture.key" :value="prefecture.key">
                {{prefecture.name}}
              </option>
            </optgroup>
        </b-select>
      </b-field>
    </b-field>

    <weekly-weather :weeklyWeathers="weeklyWeathers" :weatherCodes="weatherCodes" />
  </section>
</template>

<script>
import WeeklyWeather from './WeeklyWeather.vue';

export default {
  components: { WeeklyWeather },
  name: 'WeatherForecast',
  data: () => {
    return {
      selectedOffice: [],
      weeklyWeathers: [],
    };
  },
  props: {
    weatherCodes: {
      type: Object,
      default: () => {
        return {};
      }
    },
    officeCodes: {
      type: Object,
      default: () => {
        return {};
      }
    },
    centerCodes: {
      type: Object,
      default: () => {
        return {};
      }
    }
  },
  computed: {
    weathers: function () {
      return this.weatherCodes;
    },
    offices: function () {
      return this.officeCodes;
    },
    centers: function () {
      return Object.entries(this.centerCodes).map(([centerCode, center]) => {
        return {
          key: centerCode,
          ...center,
        };
      });
    },
    officeEntries: function() {
      return Object.entries(this.offices).map(([officeCode, office]) => {
        return {
          key: officeCode,
          ...office,
        };
      });
    }
  },
  methods: {
    getPrefectures(_parentCode) {
      return this.officeEntries.filter(item => {
        return item.parent == _parentCode;
      })
    }
  },
  watch: {
    selectedOffice: function () {
      fetch(`https://www.jma.go.jp/bosai/forecast/data/forecast/${this.selectedOffice}.json`)
        .then(data => data.json())
        .then(res => {
          const timeSeries = res.flatMap(item => item?.timeSeries);
          const filtered = timeSeries.map(item => {
            return {
              timeDefines: item?.timeDefines,
              weatherCodes: item?.areas?.find(_ => true)?.weatherCodes,
            };
          }).filter(item => item.weatherCodes);

          // 各 timeDefineとweatherCodeを統合する
          // [{timeDef, weatherCode} ... ] (length: 7) となる想定
          const pair = filtered.reduce((acc, item, _idx) => {
            if(item?.timeDefines?.length !== item?.weatherCodes?.length) return acc;

            item?.timeDefines?.forEach((_, idx) => {
              const time = new Date(item.timeDefines[idx]);
              const weatherCode = item.weatherCodes[idx];

              if(acc.length == 0 || acc.every(elem => {
                return elem.time.toDateString() != time.toDateString()
              })) {
                acc.push({
                  idx: acc.length,
                  time,
                  weatherCode
                });
              }
            });
            return acc;
          }, []);

          this.weeklyWeathers = pair;
        }).catch(error => {
          console.error(error);
        });
    }
  }
}
</script>
