<script setup>
import {onMounted, ref} from "vue";
const baseUrl = "http://localhost:8001"
const topCities = ref({});
const weatherData = ref([]);
const query = ref("");

function handleSearch() {
  if (!query.value) {
    return;
  }
  const url = `${baseUrl}/weather?location=${query.value}`;
  try {
    localStorage.setItem("city", query.value);
  }
  catch (error) {
    console.error(error);
  }
  fetch(url).then(res => res.json()).catch(error => console.log(error)).then((data) => {weatherData.value = data;});
  handleCityRank()
}

function handleCityRank() {
  const url = `${baseUrl}/cities`;
  fetch(url).then(res => res.json()).catch((error) => {console.log(error)}).then((data) => {
    topCities.value = data;
  });
}

onMounted(() => {
  handleCityRank();
  query.value = localStorage.getItem("city")
  handleSearch()
})

</script>

<template>
  <div class="menu">
    <div class="search-bar">
      <input type="text" class="head-element" placeholder="Поиск" v-model="query" @keyup.enter="handleSearch" @change="">
    </div>
    <div class="cities-top">
      <div class="head-element">Рейтинг Городов</div>
      <button class="city head-element" v-for="city in topCities" @click="() => {query = city.name; handleSearch()}">
        <div>{{city.name}}</div>  <div>{{city.count}}</div>
      </button>
    </div>
  </div>
  <div class="main">
    <div
        v-if="weatherData.length > 0"
        v-for="data in weatherData"
        class="weather-container"
    >
      <div class="weather-element time">⌚ {{ new Date(data.dt * 1000).toLocaleString("ru-RU") }}</div>
      <div class="weather-element temperature">🌡️ {{ (data.main.temp / 10).toFixed(1) }} C</div>
      <div class="weather-element humidity">💧 {{ data.main.humidity }}%</div>
      <div class="weather-element windspeed">💨 {{ data.wind.speed }} m/s</div>
    </div>
  </div>
</template>

<style scoped>
.main {
  position: relative;
  margin: 5px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  min-width: 78vw;
  max-height: 100vh;
  overflow-y: scroll;
}

.menu {
  margin: 5px;
  position: absolute;
  display: flex;
  align-items: center;
  justify-content: left;
  top: 0;
  left: 0;
  flex-direction: column;
  width: 15%;
  height: 100%;
  background-color: #F0F5FF;
  border-radius: 15px;
}

.head-element {
  margin: 5px;
  position: relative;
  width: 95%;
  height: fit-content;
  text-align: center;
  color: #F0F5FF;
  font-size: 1vw;
  border-radius: 15px;
  border: 1px solid #2A4E6B;
  background: #48A9F8;
  transition: 0.2s ease-in-out;
  padding: 5px;
}

button.head-element:hover, button.head-element:focus {
  box-shadow: 2px 2px 2px #2A4E6B;
  transform: translateY(2px);
}

.weather-container {
  display: flex;
  flex-direction: column;
  background: #F0F5FF;
  padding: 10px;
  border-radius: 15px;
  font-size: 2vw;
  color: #2A4E6B;
  min-width: 360px;
  transition: 0.2s ease-in-out;
  min-height: 270px;
}

.weather-container:hover {
  box-shadow: 2px 2px 2px #2A4E6B;
  transform: translateY(2px);
}

.weather-element {
  position: relative;
  width: 95%;
  height: fit-content;
  text-align: center;
}

.cities-top {
  display: flex;
  flex-direction: column;
  background-image: linear-gradient(160deg, #2A3B4D 0%, #1A2A3A 100%);
  border-radius: 15px;
  height: 90%;
  margin: 5px;
  width: 95%;
}
.city {
  color: #F0F5FF;
  margin: 5px;
  padding: 5px;
  border-radius: 15px;
  border: 1px solid #2A4E6B;
  display: flex;
  justify-content: space-between;
  background: #48A9F8;
}

.search-bar {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
}

</style>
