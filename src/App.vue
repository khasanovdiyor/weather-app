<template>
  <div id="app">
    <div class="container">
      <div
        class="main"
        :style="{ 'background-image': 'url(/images/' + backgrondImage + ')' }"
      >
        <div>
          <h2>{{ Math.ceil(weatherDetails.main.temp) }}°</h2>
          <div class="main__info">
            <div class="main__info__main">
              <h3>{{ weatherDetails.name }}</h3>
              <span>{{ currentTime }}</span>
            </div>
            <div class="main__info__sub">
              <img
                :src="`/icons/${weatherIcon}`"
                alt="Weather type image"
                width="40"
              />
              <div>{{ weatherDetails.weather[0].main }}</div>
            </div>
          </div>
        </div>
      </div>
      <div class="aside">
        <div>
          <div class="search-box">
            <input
              type="text"
              v-model="searchText"
              placeholder="Another location"
              @keyup.enter="currentLocation = searchText"
            />
            <span @click="currentLocation = searchText">
              <img src="/icons/loupe.png" width="25" alt="search" />
            </span>
          </div>
          <div class="cities-box">
            <ul>
              <li
                v-for="(location, idx) in locations"
                :key="idx"
                @click="currentLocation = location"
              >
                {{ location }}
              </li>
            </ul>
          </div>
          <div class="weather-details">
            <h4>Weather Details</h4>
            <div>
              <span>Cloudy</span><span>{{ weatherDetails.clouds.all }}%</span>
            </div>
            <div>
              <span>Humidity</span
              ><span>{{ weatherDetails.main.humidity }}%</span>
            </div>
            <div>
              <span>Wind</span
              ><span>{{ Math.ceil(weatherDetails.wind.speed) }} m/s</span>
            </div>
            <div>
              <span>Rain</span
              ><span
                >{{ weatherDetails.rain ? weatherDetails.rain : "0" }}mm</span
              >
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  components: {},
  data() {
    return {
      searchText: "",
      currentLocation: "Tashkent",
      locations: ["Andijan", "Samarkand", "Jizzakh", "Bukhara"],
      weatherDetails: {
        timezone: "",
        clouds: {},
        weather: [{ icon: "", main: "" }],
        main: {},
        wind: {},
      },
      backgrondImage: "",
      weatherIcon: "",
    };
  },
  watch: {
    currentLocation: async function () {
      await this.getCurrentLocationWeather();
    },
    weatherType: {
      handler: function () {
        if (this.weatherType === "Clear") {
          this.weatherIcon = "sun.svg";
          this.backgrondImage = "sunny.jpg";
        } else if (this.weatherType === "Clouds") {
          this.weatherIcon = "cloudy.svg";
          this.backgrondImage = "cloudy.jpg";
        } else if (
          this.weatherType === "Rain" ||
          this.weatherType === "Drizzle"
        ) {
          this.weatherIcon = "rain.svg";
          this.backgrondImage = "rain.jpg";
        }
      },
    },
  },
  computed: {
    currentTime() {
      const d = new Date();
      const localTime = d.getTime();
      const localOffset = d.getTimezoneOffset() * 60000;
      const utc = localTime + localOffset;
      const current = utc + 1000 * this.weatherDetails.timezone;
      const date = new Date(current);
      const [hour, minutes] = [date.getHours(), date.getMinutes()];
      var options = {
        weekday: "short",
        year: "numeric",
        month: "long",
        day: "numeric",
        hour12: false,
      };
      return (
        `${hour}:${minutes > 9 ? "" + minutes : "0" + minutes} - ` +
        date.toLocaleDateString("en-uk", options)
      );
    },
    weatherType() {
      return this.weatherDetails.weather[0].main;
    },
  },
  methods: {
    async getCurrentLocationWeather() {
      const res = await this.axios.get(process.env.VUE_APP_BASE_URL, {
        params: {
          q: this.currentLocation,
          units: "metric",
          appid: process.env.VUE_APP_API_KEY,
        },
      });
      this.weatherDetails = res.data;
    },
  },
  async mounted() {
    await this.getCurrentLocationWeather();
  },
};
</script>

<style>
</style>
