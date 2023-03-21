<script>
import { ref } from "vue";
import { cities } from "../data/cities_list.ts";
export default {
  data() {
    return {
      cities,
      name,
      temp: "",
      minTemp: "",
      maxTemp: "",
      feelLike: "",
      pressure: "",
      humidity: "",
      day: new Date().getDate(),
      month: new Date().getMonth() + 1,
      year: new Date().getFullYear(),
    };
  },
  props: {
    data: String,
  },
  setup() {
    let showMenu = ref(false);
    let show = ref(false);
    const toggleNav = () => (showMenu.value = !showMenu.value);
    return { showMenu, show, toggleNav };
  },
  methods: {
    async getCityInfo(city, country) {
      const res = await fetch(
        `http://api.openweathermap.org/geo/1.0/direct?q=${city},${country}&limit=5&appid=${
          import.meta.env.VITE_API_KEY
        }`
      );
      const cityInfo = await res.json();
      return cityInfo[0];
    },
    async getCityWeather(city, country) {
      let cityInfo = await this.getCityInfo(city, country);
      const { lat, lon } = cityInfo;
      const res = await fetch(
        `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${
          import.meta.env.VITE_API_KEY
        }`
      );
      const cityWeather = await res.json();
      return cityWeather;
    },
    async chooseCity(name, city, country) {
      const weather = await this.getCityWeather(city, country);
      this.temp = Math.round(weather.main.temp - 273.15);
      this.minTemp = Math.round(weather.main.temp_min - 273.15);
      this.maxTemp = Math.round(weather.main.temp_max - 273.15);
      this.pressure = weather.main.pressure;
      this.humidity = weather.main.humidity;
      this.feelLike = Math.round(weather.main.feels_like - 273.15);
      this.name = name;
    },
  },
  mounted() {
    const callAPi = (async () => {
      const weather = await this.getCityWeather("Ha Noi", "VN");
      this.temp = Math.round(weather.main.temp - 273.15);
      this.minTemp = Math.round(weather.main.temp_min - 273.15);
      this.maxTemp = Math.round(weather.main.temp_max - 273.15);
      this.pressure = weather.main.pressure;
      this.humidity = weather.main.humidity;
      this.feelLike = Math.round(weather.main.feels_like - 273.15);
      this.name = "Hà Nội";

      console.log(new Date());
    })();
  },
};
</script>

<template>
  <div>
    <nav
      class="px-6 py-8 bg-indigo-600 md:flex md:justify-between md:items-center"
    >
      <div class="flex items-center justify-between">
        <router-link
          to="/"
          class="text-xl font-bold text-gray-100 md:text-2xl hover:text-indigo-400"
          >Dự báo thời tiết
        </router-link>
        <!-- Mobile menu button -->
        <div @click="toggleNav" class="flex md:hidden">
          <button
            type="button"
            class="text-gray-100 hover:text-gray-400 focus:outline-none focus:text-gray-400"
          >
            <svg viewBox="0 0 24 24" class="w-6 h-6 fill-current">
              <path
                fill-rule="evenodd"
                d="M4 5h16a1 1 0 0 1 0 2H4a1 1 0 1 1 0-2zm0 6h16a1 1 0 0 1 0 2H4a1 1 0 0 1 0-2zm0 6h16a1 1 0 0 1 0 2H4a1 1 0 0 1 0-2z"
              ></path>
            </svg>
          </button>
        </div>
      </div>

      <!-- Mobile Menu open: "block", Menu closed: "hidden" -->
      <ul
        :class="showMenu ? 'flex' : 'hidden'"
        class="flex-col mt-8 space-y-4 md:flex md:space-y-0 md:flex-row md:items-center md:space-x-10 md:mt-0"
      >
        <li class="text-gray-100 hover:text-indigo-400">Trang chủ</li>
        <li>
          <div class="relative">
            <!-- Dropdown toggle button -->
            <button
              @click="show = !show"
              class="flex items-center text-indigo-100 bg-indigo-600 rounded-md focus:outline-none"
            >
              <span class="mr-4">Các tỉnh</span>
              <svg
                class="w-5 h-5 text-indigo-100"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
                fill="currentColor"
              >
                <path
                  fill-rule="evenodd"
                  d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
                  clip-rule="evenodd"
                />
              </svg>
            </button>

            <!-- Dropdown menu -->
            <div
              v-show="show"
              class="py-2 mt-2 bg-indigo-500 rounded-md shadow-xl lg:absolute lg:right-0 w-52"
            >
              <router-link
                to="/"
                class="block px-4 py-2 text-sm text-indigo-100 hover:bg-indigo-400 hover:text-indigo-100"
                v-for="city in cities"
                @click="chooseCity(city.name, city.city, city.country)"
              >
                {{ city.name + " - " + city.country }}
              </router-link>
            </div>
          </div>
        </li>
      </ul>
    </nav>
  </div>
  <div class="mx-auto p-4 bg-purple-400 h-screen flex justify-center">
    <div class="flex flex-wrap">
      <div class="w-full px-2">
        <div
          class="bg-gray-900 text-tahit relative min-w-0 break-words rounded-lg overflow-hidden shadow-sm mb-4 w-full bg-white dark:bg-gray-600"
        >
          <div class="px-6 py-6 relative">
            <div class="flex mb-4 justify-between items-center">
              <div>
                <h5 class="mb-0 font-medium text-xl">{{ name }}</h5>
                <h6 class="mb-0">{{ day + "/" + month + "/" + year }}</h6>
                <small>Cloudy</small>
              </div>
              <div class="text-right">
                <img
                  src="https://i.imgur.com/ffgW9JQ.png"
                  class="block w-8 h-8"
                />
                <h3 class="font-bold text-4xl mb-0">
                  <span>{{ temp }}&deg;</span>
                </h3>
              </div>
            </div>
            <div class="block sm:flex justify-between items-center flex-wrap">
              <div class="w-full sm:w-1/2">
                <div class="flex mb-2 justify-between items-center">
                  <span>Temp</span
                  ><small class="px-2 inline-block"
                    >{{ temp }}&nbsp;&deg;</small
                  >
                </div>
              </div>
              <div class="w-full sm:w-1/2">
                <div class="flex mb-2 justify-between items-center">
                  <span>Feels like</span
                  ><small class="px-2 inline-block"
                    >{{ feelLike }}&nbsp;&deg;</small
                  >
                </div>
              </div>
              <div class="w-full sm:w-1/2">
                <div class="flex mb-2 justify-between items-center">
                  <span>Temp min</span
                  ><small class="px-2 inline-block"
                    >{{ minTemp }}&nbsp;&deg;</small
                  >
                </div>
              </div>
              <div class="w-full sm:w-1/2">
                <div class="flex mb-2 justify-between items-center">
                  <span>Temp max</span
                  ><small class="px-2 inline-block"
                    >{{ maxTemp }}&nbsp;&deg;</small
                  >
                </div>
              </div>
              <div class="w-full sm:w-1/2">
                <div class="flex mb-2 justify-between items-center">
                  <span>pressure</span
                  ><small class="px-2 inline-block">{{ pressure }}</small>
                </div>
              </div>
              <div class="w-full sm:w-1/2">
                <div class="flex mb-2 justify-between items-center">
                  <span>humidity</span
                  ><small class="px-2 inline-block">{{ humidity }}%</small>
                </div>
              </div>
            </div>
          </div>

          <div class="px-6 py-6 relative">
            <div
              class="text-center justify-between items-center flex"
              style="flex-flow: initial"
            >
              <div
                class="text-center mb-0 flex items-center justify-center flex-col"
              >
                <span class="block my-1">Mon</span
                ><img
                  src="https://i.imgur.com/ffgW9JQ.png"
                  class="block w-8 h-8"
                /><span class="block my-1">{{ temp }}&deg;</span>
              </div>
              <div
                class="text-center mb-0 flex items-center justify-center flex-col"
              >
                <span class="block my-1">Tues</span
                ><img
                  src="https://i.imgur.com/BQbzoKt.png"
                  class="block w-8 h-8"
                /><span class="block my-1">{{ temp }}&deg;</span>
              </div>
              <div
                class="text-center mb-0 flex items-center justify-center flex-col"
              >
                <span class="block my-1">Wed</span
                ><img
                  src="https://i.imgur.com/BQbzoKt.png"
                  class="block w-8 h-8"
                /><span class="block my-1">{{ temp }}&deg;</span>
              </div>
              <div
                class="text-center mb-0 flex items-center justify-center flex-col"
              >
                <span class="block my-1">Thurs</span
                ><img
                  src="https://i.imgur.com/ffgW9JQ.png"
                  class="block w-8 h-8"
                /><span class="block my-1">{{ temp }}&deg;</span>
              </div>
              <div
                class="text-center mb-0 flex items-center justify-center flex-col"
              >
                <span class="block my-1">Fri</span
                ><img
                  src="https://i.imgur.com/ffgW9JQ.png"
                  class="block w-8 h-8"
                /><span class="block my-1">{{ temp }}&deg;</span>
              </div>
              <div
                class="text-center mb-0 flex items-center justify-center flex-col"
              >
                <span class="block my-1">Sat</span
                ><img
                  src="https://i.imgur.com/BQbzoKt.png"
                  class="block w-8 h-8"
                /><span class="block my-1">{{ temp }}&deg;</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
