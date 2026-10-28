<script setup>
import { ref, onMounted } from 'vue';

const apiKey = '4d9443fc16b3aac786cc0b8ec5057004';


const cityName = ref('Москва');
const temp = ref(0);
const description = ref('');
const humidity = ref(0);
const windSpeed = ref(0);
const rainChance = ref(0);

const isEditingCity = ref(false);
const cityInputValue = ref('');
const activeCardIndex = ref(0);


const weatherIcon = ref('/icons/icon_2.png');

const forecast = ref([
  { day: 'Ср', temp: '30 °C', icon: '/icons/Sun.svg' },
  { day: 'Чт', temp: '22 °C', icon: '/icons/icon_3.png' },
  { day: 'Пт', temp: '24 °C', icon: '/icons/icon_3.png' },
  { day: 'Сб', temp: '26 °C', icon: '/icons/Sun.svg' }
]);


function updateWeatherAssets(iconCode) {
  if (iconCode) {
    weatherIcon.value = `https://openweathermap.org/img/wn/${iconCode}@2x.png`;
  }
}


async function fetchWeatherData(city) {
  try {
    const response = await fetch(
      `https://api.openweathermap.org/data/2.5/weather?q=${city}&units=metric&lang=ru&appid=${apiKey}`
    );

    if (!response.ok) throw new Error('Город не найден');

    const data = await response.json();

    cityName.value = data.name;
    temp.value = Math.round(data.main.temp);
    
    const desc = data.weather[0].description;
    description.value = desc.charAt(0).toUpperCase() + desc.slice(1);
    
    humidity.value = data.main.humidity;
    windSpeed.value = Math.round(data.wind.speed);
    rainChance.value = data.clouds ? data.clouds.all : 0;

    updateWeatherAssets(data.weather[0].icon);

  } catch (error) {
    alert('Не удалось получить данные для этого города. Проверьте название.');
  }
}

function toggleEditCity() {
  isEditingCity.value = true;
}

function handleCitySubmit() {
  const newCity = cityInputValue.value.trim();
  if (newCity) {
    fetchWeatherData(newCity);
    cityInputValue.value = '';
  }
  isEditingCity.value = false;
}

function selectCard(index) {
  activeCardIndex.value = index;
}

onMounted(() => {
  fetchWeatherData('Moscow');
});
</script>

<template>
  <main>
    <section class="back_1">
      <div class="container">
        <div class="weather_block">
          <div class="weather_itm">
            
            
            <div class="weather_left">
              <div class="weather_p">
                <div class="top">
                  <p class="txt_1">Вторник</p>
                  <p class="txt_2">20 июня 2025</p>

                  <div class="loc_bi">
                    <div class="icon_loc">
                      <img src="/icons/icon_1.png" alt="gps">
                    </div>
                    <p class="txt_3">{{ cityName }}</p>
                  </div>
                </div>

                <div class="bottom">
                  <div class="icon_btm">
                    <img :src="weatherIcon" :alt="description">
                  </div>
                  <p class="txt_4">{{ temp }} °C</p>
                  <p class="txt_5">{{ description }}</p>
                </div>
              </div>
            </div>
            
            
            <div class="info_r">
              <ul class="list_info">
                <li class="inf">
                  <p class="f_1">ВЛАЖНОСТЬ:</p>
                  <p class="f_2">{{ humidity }}%</p>
                </li>
                <li class="inf">
                  <p class="f_1">ОБЛАЧНОСТЬ:</p>
                  <p class="f_2">{{ rainChance }}%</p>
                </li>
                <li class="inf">
                  <p class="f_1">ВЕТЕР:</p>
                  <p class="f_2">{{ windSpeed }} м/с</p>
                </li>
              </ul>

              <div class="weather_itm_b">
                <div 
                  v-for="(item, index) in forecast" 
                  :key="index"
                  class="weather_elm"
                  :class="{ actv: activeCardIndex === index }"
                  @click="selectCard(index)"
                >
                  <div class="icon_2">
                    <img :src="item.icon" alt="">
                  </div>
                  <p :class="activeCardIndex === index ? 'txt_6s' : 'txt_6'">{{ item.day }}</p>
                  <p :class="activeCardIndex === index ? 'txt_7s' : 'txt_7'">{{ item.temp }}</p>
                </div>
              </div>

              <div class="city_search_wrapper">
                <button 
                  v-if="!isEditingCity" 
                  class="btn_1" 
                  @click="toggleEditCity"
                >
                  <div class="icon_btn">
                    <img src="/icons/icon_4.png" alt="">
                  </div>
                  Изменить город
                </button>

                <form 
                  v-else 
                  class="city_form" 
                  @submit.prevent="handleCitySubmit"
                >
                  <input 
                    v-model="cityInputValue" 
                    type="text" 
                    class="city_input" 
                    placeholder="Введите город..."
                    autofocus
                  >
                  <button type="submit" class="btn_save">Сохранить</button>
                </form>
              </div>

            </div>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>