<template>
  <div class="weather-app">
    <h1>Weather App</h1>

    <input v-model="city" placeholder="Enter city name" />
    <button @click="fetchWeather" :disabled="loading">
      {{ loading ? "Loading..." : "Get Weather" }}
    </button>

    <p v-if="error" style="color: red;">{{ error }}</p>

    <div v-if="weatherData">
      <h2>{{ weatherData.name }}</h2>
      <p>Condition: {{ weatherData.weather[0].description }}</p>
      <p>Temperature: {{ weatherData.main.temp }} °C</p>
      <p>Humidity: {{ weatherData.main.humidity }}%</p>
      <p>Wind Speed: {{ weatherData.wind.speed }} m/s</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      city: '',
      weatherData: null,
      error: '',
      loading: false,
      apiKey: '0fe35bb746f06263be4c158b6bdaf4b9'
    };
  },
  methods: {
    async fetchWeather() {
      this.error = '';
      this.weatherData = null;
      this.loading = true;

      if (!this.city) {
        this.error = "Please enter a city name.";
        this.loading = false;
        return;
      }

      const url = `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${this.apiKey}&units=metric`;

      try {
        const response = await fetch(url);
        const data = await response.json();

        if (response.ok) {
          this.weatherData = data;
        } else {
          this.error = `API Error: ${data.message}`;
        }
      } catch (error) {
        this.error = "Network error: " + error.message;
        console.error(error);
      } finally {
        this.loading = false;
      }
    }
  }
};
</script>

<style scoped>
.weather-app {
  max-width: 400px;
  margin: 50px auto;
  text-align: center;
  font-family: Arial, sans-serif;
}

input {
  padding: 10px;
  width: 70%;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
}

button {
  padding: 10px 20px;
  cursor: pointer;
  background-color: #4285f4;
  color: white;
  border: none;
  border-radius: 8px;
  font-weight: bold;
}

button:disabled {
  background-color: #999;
  cursor: not-allowed;
}

h2 {
  margin-top: 20px;
}
</style>
