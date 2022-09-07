<template>
  <div></div>
</template>

<script setup>
import axios from "axios";
import useRoute from "vue-router";

const route = useRoute();
const apiKey = "82402898394dcbf6b480dc45f66830e4";
const getWeatherDate = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=${apiKey}&units=imperial`
    );

    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.current.dt * 1000 + localOffset;
    weatherData.data.currentTime =
      utc + 1000 * weatherData.data.timezone_offset;

    weatherData.data.hourly.forEach((hour) => {
      const utc = hour.current.dt * 1000 + localOffset;
      hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
    });
  } catch (err) {
    console.log(err);
  }
};
</script>
