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
      <ul
        v-if="mapboxSearchResults"
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
      >
        <p v-if="searchError">Someting Went wrong!</p>
        <p v-if="!searchError && mapboxSearchResults.length === 0">
          No results Found!
        </p>
        <template v-else>
          <li
            v-for="searchResult in mapboxSearchResults"
            @click="previewCity(searchResult)"
            :key="searchResult.id"
            class="py-2 cursor-pointer"
          >
            {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";

const router = useRouter();

const search = ref("");
const queryTimeOut = ref(null);
const mapBoxKey =
  "pk.eyJ1Ijoic3VtYW5rYXRoZXQiLCJhIjoiY2w3cWd2aWg2MDU4eDN2bXFzbG5vZ3RsNSJ9.ObaQp138MxYagxtvP-WH0Q";
const mapboxSearchResults = ref(null);
const searchError = ref(false);

const getSearchResults = () => {
  clearTimeout(queryTimeOut.value);
  queryTimeOut.value = setTimeout(async () => {
    if (search.value !== "") {
      try {
        const results = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${search.value}.json?access_token=${mapBoxKey}&types=place`
        );
        mapboxSearchResults.value = results.data.features;
        searchError.value = false;
      } catch {
        searchError.value = true;
      }

      return;
    }
    mapboxSearchResults.value = null;
  }, 300);
};

const previewCity = (searchResult) => {
  const [city, state] = searchResult.place_name.split(",");
  router.push({
    name: "cityView",
    params: { state: state.replaceAll(" ", ""), city: city },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    },
  });
};
</script>
