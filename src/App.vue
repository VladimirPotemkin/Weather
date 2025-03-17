<script setup>
import { ref, computed } from 'vue';

const location = ref('');
const temperature = ref(null);
const weather = ref('');
const loading = ref(false);
const error = ref(false);
const searchQuery = ref('');

const weatherClass = computed(() => {
  const condition = weather.value.toLowerCase();
  if (condition.includes('sunny')) return 'sunny';
  if (condition.includes('rain') || condition.includes('drizzle')) return 'rainy';
  if (condition.includes('cloud') || condition.includes('mist')) return 'cloudly';
  if (condition.includes('snow')) return 'snow';
  return '';
});

const getWeather = async () => {
  loading.value = true;
  error.value = false;
  
  try {
    const response = await fetch(
      `http://api.weatherapi.com/v1/current.json?key=9ce87d3a70ad4a899fc100156251703&q=${searchQuery.value}`
    );
    
    if (!response.ok) throw new Error('Город не найден');
    
    const data = await response.json();
    location.value = data.location.name;
    temperature.value = data.current.temp_c;
    weather.value = data.current.condition.text;
    
  } catch (err) {
    error.value = true;
    console.error(err);
  } finally {
    loading.value = false;
    resetSearchQuery();
  }
};

const resetSearchQuery = () => {
  searchQuery.value = '';
};
</script>

<template>
  <div class="weather" :class="weatherClass">
    <div class="container">
      <div class="card weather-form">
        <input
          v-model="searchQuery"
          type="text"
          class="weather-form__input"
          placeholder="Название города"
          @keyup.enter="getWeather"
        />
        <button @click="getWeather" class="weather-form__btn">Найти</button>
      </div>

      <div class="card weather-load" v-if="loading">Загрузка...</div>

      <div class="weather-info" v-if="!error && location && temperature">
        <div class="weather-info__text">
          <p class="card">{{ location }}</p>
          <p class="card">{{ temperature }} °C</p>
          <p class="card">{{ weather }}</p>
        </div>
      </div>

      <div class="card" v-if="error">Ошибка загрузки данных</div>
    </div>

    <div class="weather-bg">
      <div>
        <img class="weather-bg__img bg" src="./assets/main-bg.png" alt="main-bg" />
        <img class="weather-bg__img sunny" src="./assets/sunny.jpg" alt="sunny" />
        <img class="weather-bg__img rainy" src="./assets/rainy.jpg" alt="rainy" />
        <img class="weather-bg__img cloudly" src="./assets/cloudy.jpeg" alt="cloudy" />
        <img class="weather-bg__img snow" src="./assets/snow.jpg" alt="snow" />
      </div>
    </div>
  </div>
</template>