<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        v-model="search"
        @input="getSearchResults"
        type="text"
        placeholder="Search for a city or a state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />
    </div>
  </main>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const search = ref("");
const queryTimeOut = ref(null);
const mapBoxKey =
  "pk.eyJ1Ijoic3VtYW5rYXRoZXQiLCJhIjoiY2w3cWd2aWg2MDU4eDN2bXFzbG5vZ3RsNSJ9.ObaQp138MxYagxtvP-WH0Q";
const mapboxSearchResults = ref(null);

const getSearchResults = () => {
  clearTimeout(queryTimeOut.value);
  queryTimeOut.value = setTimeout(async () => {
    if (search.value !== "") {
      const results = await axios.get(
        `https://api.mapbox.com/geocoding/v5/mapbox.places/${search}.json?access_token=${mapBoxKey}&types=place`
      );
      mapboxSearchResults.value = results.data.features;
      console.log(mapboxSearchResults.value);

      return;
    }
    mapboxSearchResults.value = null;
  }, 300);
};
</script>
