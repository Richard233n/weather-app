<template>
    <div>
        <h2>Current Weather</h2>
        <div class="search-container">
            <input type="text" v-model="cityName" placeholder="Enter city name" class="search-input" />
            <button @click="fetchWeatherData" class="search-button">获取天气</button>
        </div>
        <div v-if="loading">Loading...</div>
        <div v-else>
            <div v-if="weatherData" class="card">
                <div class="left">
                    <!-- 这里可以放置天气图片 -->
                    <h3>{{ weatherData.temperature }}°C</h3>
                    <p>{{ weatherData.weather }}</p>
                </div>
                <div class="right">
                    <div class="right-content">
                        <h3>{{ weatherData.city }}</h3>
                        <p>风速: {{ weatherData.wind }}</p>
                        <p>湿度: {{ weatherData.humidity }}</p>
                    </div>
                </div>
            </div>
            <div v-else>
                <p>No weather data available</p>
            </div>
        </div>
    </div>
</template>

<script>
import { ref } from "vue";
import axios from "axios";

export default {
    name: "Weather-",
    setup() {
        const weatherData = ref(null);
        const loading = ref(false);
        const cityName = ref("");

        const fetchWeatherData = async () => {
            loading.value = true;
            try {
                const response = await axios.get(
                    `https://restapi.amap.com/v3/weather/weatherInfo?key=53b4515e9b92fc1047aee40479ec662d&city=${encodeURIComponent(
                        cityName.value
                    )}`
                );
                const data = response.data;
                if (data.lives && data.lives.length > 0) {
                    weatherData.value = {
                        city: data.lives[0].city,
                        temperature: data.lives[0].temperature,
                        weather: data.lives[0].weather,
                        wind: data.lives[0].windpower,
                        humidity: data.lives[0].humidity,
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

        return {
            weatherData,
            loading,
            cityName,
            fetchWeatherData,
        };
    },
};
</script>

<style scoped>
.search-container {
    margin-bottom: 20px;
}

.search-input {
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    font-size: 16px;
    width: 200px;
}

.search-button {
    padding: 10px 20px;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 16px;
}

.search-button:hover {
    background-color: #0056b3;
}

.card {
    border: 1px solid #ddd;
    border-radius: 10px;
    padding: 20px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    background-color: #f9f9f9;
    display: flex;
}

.left {
    flex: 1;
    text-align: center;
}

.right {
    flex: 1;
}

.right-content {
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

.card h3 {
    margin-top: 0;
    font-size: 24px;
    color: #333;
}

.card p {
    margin: 10px 0;
    font-size: 18px;
    color: #666;
}
</style>
