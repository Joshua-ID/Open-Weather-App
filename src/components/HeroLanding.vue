<template>
  <div class="hero-container">
    <Header />
    <!-- Input Section -->
    <div class="input-and-error-msg">
      <div class="input-container">
        <input
          v-model="city"
          type="text"
          placeholder="Enter city"
          @keyup.enter="fetchWeather"
        />
        <button @click="fetchWeather">Get Weather</button>
      </div>

      <!-- Error Message -->
      <span v-if="error" class="error">{{ error }}</span>
    </div>

    <!-- Weather Details Section -->
    <div v-if="weatherData" class="weather-container">
      <div class="location-and-icon">
        <h2>{{ weatherData.name }}, {{ weatherData.sys.country }}</h2>
        <!-- Weather Icon -->
        <span class="weather-icon">
          <img
            :src="getWeatherIconUrl(weatherData.weather[0].icon)"
            alt="Weather Icon"
          />
        </span>
        <div class="description">
          <p>{{ weatherData.weather[0].description }}</p>
          <p>Latitude: {{ weatherData.coord.lat }}</p>
          <p>Longitude: {{ weatherData.coord.lon }}</p>
        </div>
      </div>

      <!-- Temperature Section -->
      <div class="sections-wrapper">
        <div class="section">
          <h3>Temperature</h3>
          <p>Current: {{ weatherData.main.temp }}°C</p>
          <p>Feels Like: {{ weatherData.main.feels_like }}°C</p>
        </div>
        <!-- Weather Description Section -->
        <div class="section">
          <h3>Weather Conditions</h3>
          <p>Humidity: {{ weatherData.main.humidity }}%</p>
          <p>Pressure: {{ weatherData.main.pressure }} hPa</p>
          <p>Cloudiness: {{ weatherData.clouds.all }}%</p>
        </div>
        <!-- Wind Details Section -->
        <div class="section">
          <h3>Wind</h3>
          <p>Speed: {{ weatherData.wind.speed }} m/s</p>
          <p>Direction: {{ getWindDirection(weatherData.wind.deg) }}</p>
        </div>
        <!-- Sunrise and Sunset -->
        <div class="section">
          <h3>Sunrise & Sunset</h3>
          <p>Sunrise: {{ formatTime(weatherData.sys.sunrise) }}</p>
          <p>Sunset: {{ formatTime(weatherData.sys.sunset) }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Header from "./Header.vue";

export default {
  name: "HelloWorld",
  components: { Header },
  data() {
    return {
      city: "",
      weatherData: null,
      error: "",
      latitude: null,
      longitude: null,
      apiKey: import.meta.env.VITE_API_KEY,
    };
  },
  methods: {
    // Fetch weather data using city name
    async fetchWeather() {
      if (!this.city) {
        this.error = "Please enter a city name.";
        this.weatherData = null;
        return;
      }

      try {
        const response = await axios.get(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${this.apiKey}&units=metric`
        );
        this.weatherData = response.data;
        this.error = "";
      } catch (err) {
        this.weatherData = null;
        this.error = "Unable to fetch weather. Please check the city name.";
      }
    },

    async fetchWeatherForecast() {
      if (!this.city) {
        console.log(this.forecastData.list);
        console.log(this.forecastData.city);

        try {
          const response = await axios.get(
            `https://api.openweathermap.org/data/2.5/forecast?lat=${this.latitude}&lon=${this.longitude}&appid=${this.apiKey}&units=metric`
          );
          this.forecastData = response.data;
          this.error = "";
        } catch (err) {
          this.forecastData = null;
          this.error =
            "Unable to fetch weather forecast based on your location.";
        }
      } else {
        this.error = "Unable to retrieve location.";
      }
    },

    // Fetch weather data using geolocation (latitude and longitude)
    async fetchWeatherByLocation() {
      if (this.latitude && this.longitude) {
        try {
          const response = await axios.get(
            `https://api.openweathermap.org/data/2.5/weather?lat=${this.latitude}&lon=${this.longitude}&appid=${this.apiKey}&units=metric`
          );
          this.weatherData = response.data;
          this.error = "";
        } catch (err) {
          this.weatherData = null;
          this.error = "Unable to fetch weather based on your location.";
        }
      } else {
        this.error = "Unable to retrieve location.";
      }
    },

    // // Helper to format timestamps to readable time
    formatTime(timestamp) {
      const date = new Date(timestamp * 1000);
      return date.toLocaleTimeString("en-US", {
        hour: "2-digit",
        minute: "2-digit",
        hour12: true,
      });
    },

    // Helper to convert wind direction from degrees to compass direction
    getWindDirection(degree) {
      const directions = [
        "North",
        "North-East",
        "East",
        "South-East",
        "South",
        "South-West",
        "West",
        "North-West",
      ];
      const index = Math.round(degree / 45) % 8;
      return directions[index];
    },

    // Get the user's current location
    getLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            this.latitude = position.coords.latitude;
            this.longitude = position.coords.longitude;
            this.fetchWeatherByLocation();
            this.fetchWeatherForecast();
          },
          () => {
            this.error = "Location access denied or unavailable.";
          }
        );
      } else {
        this.error = "Geolocation is not supported by this browser.";
      }
    },

    // Get the weather icon URL from OpenWeather based on the icon code
    getWeatherIconUrl(iconCode) {
      return `https://openweathermap.org/img/wn/${iconCode}@2x.png`;
    },
  },

  // Fetch weather data based on location when component mounts
  mounted() {
    this.getLocation();
  },
};
</script>
<style>
.hero-container {
  padding: 2rem 1rem;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;

  .input-and-error-msg {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    gap: 1rem;

    .input-container {
      margin-top: 1rem;
      display: flex;
      justify-content: center;
      width: 100%;

      input {
        padding-left: 1rem;
        width: 40%;
        border: none;
        border-radius: 15px 0 0 15px;
        outline: none;
        color: #000;
        font-size: clamp(0.9375rem, 0.8378rem + 0.3191vw, 1.125rem);

        @media (max-device-width: 860.98px) {
          width: 60%;
        }
      }

      button {
        border: none;
        outline: none;
        background: rgb(92, 92, 228);
        padding: 1rem;
        cursor: pointer;

        &:hover {
          opacity: 0.8;
        }
      }
    }

    .error {
      color: red;
      font-size: clamp(0.9375rem, 0.871rem + 0.2128vw, 1.0625rem);
      text-shadow: 1px 3px 4px #000;
      font-weight: 700;
    }
  }

  .weather-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 2em;

    .location-and-icon {
      display: flex;
      align-items: center;

      .description {
        display: flex;
        flex-direction: column;
        gap: 0.6rem;
      }
    }

    .sections-wrapper {
      display: flex;
      justify-content: space-evenly;
      gap: 4rem;

      @media (max-device-width: 860.98px) {
        gap: 2em;
      }

      @media (max-device-width: 699.98px) {
        display: grid;
        row-gap: 1.5rem;
        justify-items: center;
        grid-template-columns: repeat(2, 1fr);
      }

      @media (max-device-width: 399.98px) {
        display: grid;
        column-gap: 2rem;
        row-gap: 1rem;
        grid-template-columns: repeat(2, 1fr);
      }

      .section {
        display: flex;
        flex-direction: column;
        gap: 0.5rem;

        @media (max-device-width: 699.98px) {
          text-align: center;
        }

        h3 {
          color: rgb(92, 92, 228);
          text-shadow: 1px 3px 4px #000;
          margin-bottom: 1rem;
          font-size: clamp(1rem, 0.9335rem + 0.2128vw, 1.125rem);

          @media (max-device-width: 680px) {
            margin-bottom: 0.5rem;
          }
        }

        p {
          font-size: clamp(0.9375rem, 0.9043rem + 0.1064vw, 1rem);
          white-space: nowrap;
        }
      }
    }
  }
}
</style>
