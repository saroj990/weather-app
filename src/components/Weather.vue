<script>
import { reactive, toRefs } from 'vue';

export default {
  setup() {
    const state = reactive({
      location: "",
      data: null,
      isLoading: false
    });

    const searchWeather = () => {
      const { location } = state;
      const apiKey = "6a31ef0d1b5717721fd00952caa0afd5";
      state.isLoading = true;

      if (!location) return;

      const url = `https://api.openweathermap.org/data/2.5/weather?q=${location}&appid=${apiKey}&units=metric`;
      try {
        fetch(url)
        .then(res => res.json())
        .then(data => state.data = data)
        .catch(err => {throw err });
      } catch(err) {
        console.log(err);
      } finally {
        state.isLoading = false;
      }
      
    };

    const handleChange = (e) => {
      state.location = e.target.value;
    }


    return {...toRefs(state), searchWeather, handleChange };
  }
}
</script>

<template>
  <div class="container min-h-screen">
    <div class="h-screen">
      <div class="flex h-full w-full flex-col items-center justify-center gap-2 p-6">
        <div class="text-4xl text-white font-regular pb-6">Welcome to Accu Weather</div>
        <input
            type="text"
            class="hover:ring-blue-500 rounded-lg text-md p-2 shadow-md w-1/3"
            v-model="location"
            placeholder="search your city name" @keypress.enter="searchWeather"
          >
        <div v-if="isLoading" class="text-base text-white pt-5"> Loading weather info...</div>
        <div v-if="!isLoading && data" class="text-center">
          <div class="min-96 p-1">
            <div class="pt-2 text-white font-medium text-3xl">
              {{ data.name }}, {{data.sys.country}}
            </div>
          </div>
          <div class="p-1 font-medium text-2xl text-white capitalize">
          {{data.weather[0].main}}, {{ data.weather[0].description}}
          </div>

          <div class="p-1 font-medium text-xl text-white capitalize">
          {{data.coord.lon.toFixed(2)}}, {{ data.coord.lat.toFixed(2) }}
          </div>

          <div class="p-1 font-medium text-xl text-white capitalize">
            <span class="text-white">
              Temp:          
            </span>{{data.main.temp.toFixed(2)}} ° <span class="font-bold text-base">C</span>, 
            <span class="pl-2 text-green-200"> Min: </span>{{ data.main.temp_min }} ° <span class="font-bold text-base">C</span>,
            <span class="pl-2 text-yellow-500"> Max: </span>{{ data.main.temp_max }} °<span class=" text-base font-bold">C</span>
            </div>
        </div>
        
      </div>
    </div>
  </div>
</template>
