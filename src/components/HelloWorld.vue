<template>
  <div class="weather-container" :class="weatherClass">
    <audio ref="weatherAudio" :src="weatherSound" autoplay loop></audio>

    <div class="weather-box">
      <h1 class="location">{{ weatherData?.name || "Enter a City" }}</h1>
      <p class="condition">{{ weatherData?.weather[0].main || "" }}</p>
      <p class="temperature">
        {{ weatherData ? Math.round(weatherData.main.temp) + "°C" : "--°C" }}
      </p>

      <div class="details" v-if="weatherData">
        <div class="detail-row">
          <span>Humidity:</span>
          <span>{{ weatherData.main.humidity }}%</span>
        </div>
        <div class="detail-row">
          <span>Wind:</span>
          <span>{{ weatherData.wind.speed }} m/s</span>
        </div>
        <div class="detail-row">
          <span>Sunrise:</span>
          <span>{{ formatTime(weatherData.sys.sunrise) }}</span>
        </div>
        <div class="detail-row">
          <span>Sunset:</span>
          <span>{{ formatTime(weatherData.sys.sunset) }}</span>
        </div>
      </div>

      <div class="search">
        <input v-model="city" placeholder="Enter city..." />
        <button @click="fetchWeather">Search</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      city: '',
      weatherData: null,
      apiKey: '0fe35bb746f06263be4c158b6bdaf4b9',
      weatherSound: ''
    };
  },
  computed: {
    weatherClass() {
      if (!this.weatherData) return 'default';
      const desc = this.weatherData.weather[0].description.toLowerCase();
      if (desc.includes('clear')) return 'sunny';
      if (desc.includes('cloud')) return 'cloudy';
      if (desc.includes('rain') || desc.includes('drizzle')) return 'rainy';
      if (desc.includes('snow')) return 'snowy';
      if (desc.includes('storm') || desc.includes('thunder')) return 'stormy';
      return 'default';
    }
  },
  watch: {
    weatherClass(newClass) {
      const soundMap = {
        sunny: require('@/assets/sunny.mp3'),
        cloudy: require('@/assets/cloudy.mp3'),
        rainy: require('@/assets/rain.mp3'),
        snowy: require('@/assets/snow.mp3'),
        stormy: require('@/assets/storm.mp3'),
        default: ''
      };

      this.weatherSound = soundMap[newClass] || '';
      this.$nextTick(() => {
        const audio = this.$refs.weatherAudio;
        if (audio) {
          audio.pause();
          audio.load();
          audio.play().catch(() => {
          });
        }
      });
    }
  },
  methods: {
    async fetchWeather() {
      if (!this.city) return;
      const url = `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=${this.apiKey}&units=metric`;

      try {
        const res = await fetch(url);
        const data = await res.json();
        if (res.ok) this.weatherData = data;
        else alert('City not found.');
      } catch (err) {
        console.error('Error:', err);
      }
    },
    formatTime(unix) {
      return new Date(unix * 1000).toLocaleTimeString([], {
        hour: '2-digit',
        minute: '2-digit'
      });
    }
  }
};
</script>

<style scoped>
.weather-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  font-family: 'Segoe UI', sans-serif;
  transition: background 0.5s ease;
  padding: 20px;
}

.weather-box {
  background: rgba(255, 255, 255, 0.1);
  padding: 40px;
  border-radius: 20px;
  text-align: center;
  color: white;
  width: 100%;
  max-width: 400px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(10px);
}

.location {
  font-size: 2rem;
  margin-bottom: 10px;
}

.condition {
  font-size: 1.2rem;
  margin-bottom: 10px;
}

.temperature {
  font-size: 4rem;
  font-weight: 600;
  margin-bottom: 20px;
}

.details {
  margin: 20px 0;
  text-align: left;
}

.detail-row {
  display: flex;
  justify-content: space-between;
  padding: 8px 0;
  border-bottom: 1px solid rgba(255, 255, 255, 0.2);
  font-size: 1rem;
}

.search {
  display: flex;
  gap: 10px;
  margin-top: 20px;
}

.search input {
  flex: 1;
  padding: 10px;
  border-radius: 8px;
  border: none;
  font-size: 1rem;
}

.search button {
  background: #ffffff;
  color: #333;
  border: none;
  padding: 10px 20px;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
  transition: 0.3s ease;
}

.search button:hover {
  background: #ddd;
}

.sunny {
  background-image: url(../assets/sunny.png);
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
}

.cloudy {
  background-image: url(../assets/cloudy.jpg);
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
}

.rainy {
  background-image: url(../assets/rainy.jpg);
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
}

.snowy {
  background-image: url(../assets/snowy.jpg);
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
}

.stormy {
  background-image: url(../assets/stormy.jpeg);
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
}

.default {
  background: linear-gradient(to bottom, #1e3c72, #2a5298);
}
</style>
