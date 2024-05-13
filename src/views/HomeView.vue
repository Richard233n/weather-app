<template>
  <div>
    <h2>Current Weather</h2>
    <div>
      <input type="text" v-model="cityName" placeholder="Enter city name" />
      <button @click="fetchWeatherData">Get Weather</button>
    </div>
    <div v-if="loading">Loading...</div>
    <div v-else-if="weatherData">
      <p>{{ weatherData.city }} Weather</p>
      <p>Temperature: {{ weatherData.temperature }}°C</p>
      <p>Weather: {{ weatherData.weather }}</p>
    </div>
    <div v-else>
      <p>No weather data available</p>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import axios from "axios";

export default {
  name: "Weather-",
  setup() {
    // 使用 ref 创建响应式数据
    const weatherData = ref(null);
    const loading = ref(false);
    const cityName = ref("");

    // 定义异步方法以获取天气数据
    const fetchWeatherData = async () => {
      loading.value = true;
      try {
        // 使用 axios 发起异步请求
        const response = await axios.get(
          `https://restapi.amap.com/v3/weather/weatherInfo?key=53b4515e9b92fc1047aee40479ec662d&city=${encodeURIComponent(
            cityName.value
          )}`
        );
        const data = response.data;
        if (data.lives && data.lives.length > 0) {
          // 更新天气数据
          weatherData.value = {
            city: data.lives[0].city,
            temperature: data.lives[0].temperature,
            weather: data.lives[0].weather,
          };
        } else {
          console.error("Weather data is empty");
        }
      } catch (error) {
        console.error("Error fetching weather data:", error);
      } finally {
        loading.value = false;
      }
    };

    // 返回数据和方法以在模板中使用
    return {
      weatherData,
      loading,
      cityName,
      fetchWeatherData,
    };
  },
};
</script>
