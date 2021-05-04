<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 70 ? 'warm' : ''">
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search..."
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>
      <div class="weather-wrap" v-if="typeof weather.main !== 'undefined'">
        <div class="text-white text-center">
          <div class="text-4xl font-medium text-shadow-sm">{{ weather.name }}</div>
          <div class="text-2xl font-light italic">
            {{ date.weekday }} {{ date.month }} {{ date.day }}
          </div>
        </div>
        <div class="weather-box max-w-min mx-auto text-center my-2 p-7 bg-white bg-opacity-25 shadow-md hover:shadow-lg text-white rounded-lg text-shadow-sm">
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
      date: {
        weekday: Date().split(" ")[0],
        month: Date().split(" ")[1],
        day: Date().split(" ")[2],
      },
    };
  },
  methods: {
    fetchWeather(e) {
      if (e.key == "Enter") {
        const reqURL = `${this.url_base}weather?q=${this.query}&units=imperial&appid=${this.api_key}`;
        fetch(reqURL)
          .then((res) => {
            return res.json();
          })
          .then((res) => {
            console.log(res);
            if (res.cod !== 200) {
              throw Error(res);
            }
            return res;
          })
          .then(this.setResults)
          .catch((err) => {
            console.error(err);
          });
      }
    },
    setResults(res) {
      this.weather = res;
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}

#app {
  /* Cold background */
  background: rgb(2, 0, 36);
  background: linear-gradient(
    90deg,
    rgba(2, 0, 36, 1) 0%,
    rgba(9, 9, 121, 1) 35%,
    rgba(0, 212, 255, 1) 100%
  );
}
#app.warm {
  /* Warm Background */
  background: rgb(253, 29, 29);
  background-image: linear-gradient(
    109.6deg,
    rgba(255, 174, 0, 1) 11.2%,
    rgba(255, 0, 0, 1) 100.2%
  );
  transition: 0.4s;
}

main {
  min-height: 100vh;
  padding: 25px;

  background: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;

  color: #313131;
  font-size: 20px;

  appearance: none;
  border: none;
  outline: none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px;
}


</style>
