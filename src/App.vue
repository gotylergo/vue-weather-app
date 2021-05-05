<template>
  <div
    id="app"
    :class="
      typeof weather.main !== 'undefined' && weather.main.temp > 70
        ? 'bg-gradient-to-r from-yellow-700 to-red-800'
        : 'bg-gradient-to-r from-blue-900  to-blue-700'
    "
  >
    <main class="p-6 font-sans">
      <div class="w-full pb-8">
        <input
          type="text"
          class="block w-full p-3 text-xl appearance-none border-none outline-none bg-none shadow-md focus:shadow-lg rounded-bl-lg focus:rounded-bl-none	rounded-tr-lg focus:rounded-tr-none focus:rounded-tl-lg focus:rounded-br-lg text-gray-800 placeholder-gray-500 bg-white bg-opacity-50 focus:bg-opacity-75 transition-all"
          placeholder="Search..."
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>
      <div
        class="text-white text-center"
        v-if="error !== ''"
      >
        Error. Could not retrieve weather.
      </div>
      <div v-if="typeof weather.main !== 'undefined'">
        <div class="text-white text-center">
          <div class="text-4xl font-medium text-shadow-sm">
            {{ weather.name }}
          </div>
          <div class="text-2xl font-light italic">
            {{ date.weekday }} {{ date.month }} {{ date.day }}
          </div>
        </div>
        <div
          class="max-w-min mx-auto text-center my-2 p-7 bg-white bg-opacity-25 shadow-md hover:shadow-lg text-white rounded-lg text-shadow-sm"
        >
          <div class="text-7xl">{{ Math.round(weather.main.temp) }}&deg;f</div>
          <div class="text-4xl italic">{{ weather.weather[0].main }}</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      api_key: process.env.VUE_APP_API_KEY,
      url_base: "//api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
      error: "",
      date: {
        weekday: Date().split(" ")[0],
        month: Date().split(" ")[1],
        day: Date().split(" ")[2],
      },
    };
  },
  methods: {
    fetchWeather(e) {
      if (e.key == 'Enter') {
        const reqURL = `${this.url_base}weather?q=${this.query}&units=imperial&appid=${this.api_key}`;
        fetch(reqURL)
          .then((res) => {
            return res.json();
          })
          .then((res) => {
            if (res.cod !== 200) {
              throw Error(res);
            }
            this.setError('');
            return res;
          })
          .then(this.setResults)
          .catch((err) => {
            console.error(err);
            this.setResults('');
            this.setError(err);
          });
      }
    },
    setResults(res) {
      this.weather = res;
    },
    setError(err) {
      this.error = err;
    },
  },
};
</script>

<style>

main {
  min-height: 100vh;
}

</style>
