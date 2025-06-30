<template>
  <div class="weather-app" :class="weatherClass">
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
  computed: {
    weatherClass() {
      if (!this.weatherData) return '';
      const desc = this.weatherData.weather[0].description.toLowerCase();

      if (desc.includes('clear')) return 'sunny';
      if (desc.includes('cloud')) return 'cloudy';
      if (desc.includes('rain')) return 'rainy';
      if (desc.includes('drizzle')) return 'rainy';
      if (desc.includes('thunderstorm') || desc.includes('storm')) return 'stormy';
      if (desc.includes('snow')) return 'snowy';
      if (desc.includes('mist') || desc.includes('fog') || desc.includes('haze') || desc.includes('smoke')) return 'foggy';
      if (desc.includes('dust') || desc.includes('sand') || desc.includes('ash')) return 'dusty';
      if (desc.includes('tornado')) return 'tornado';
      if (desc.includes('squall')) return 'windy';

      return 'default-weather';
    }
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

      const url = `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=0fe35bb746f06263be4c158b6bdaf4b9&units=metric`;

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
  padding: 20px;
  border-radius: 12px;
  transition: background 0.5s ease;
  color: #fff;
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

.sunny {
  background: linear-gradient(to top, #fceabb, #f8b500); 
  color: #333;
}

.cloudy {
  background: linear-gradient(to top, #d7d2cc, #304352); 
}

.rainy {
  background: linear-gradient(to top, #4b79a1, #283e51); 
}

.snowy {
  background: linear-gradient(to top, #e6dada, #274046); 
  color: #333;
}

.stormy {
  background: linear-gradient(to top, #2c3e50, #000000); 
}

.foggy {
  background: linear-gradient(to top, #bdc3c7, #2c3e50); 
}

.dusty {
  background: linear-gradient(to top, #d1913c, #ffd194); 
  color: #333;
}

.tornado {
  background: linear-gradient(to top, #232526, #414345); 
}

.windy {
  background: linear-gradient(to top, #83a4d4, #b6fbff); 
  color: #333;
}

.default-weather {
  background: #7f8c8d; 
}
</style>
