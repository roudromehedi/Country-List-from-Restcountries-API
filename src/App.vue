<script setup>
import { ref, watch, computed, onMounted } from "vue";

const countries = ref([]);

async function getAllCountries(list) {
  const response = await fetch(
    "https://restcountries.com/v3.1/all?fields=name,capital,flags"
  );
  const data = await response.json();

  const filteredCountries = data
    .filter((c) => c.name.common.toLowerCase().startsWith(list.toLowerCase()))
    .map((country) => ({
      name: country.name.common,
      capital: country.capital[0],
      flag: country.flags.svg,
    }));

  countries.value = filteredCountries;
}

const name = ref("");
const filteredCountries = computed(() => countries.value);

watch(name, (newValue) => {
  getAllCountries(newValue);
});

onMounted(() => {
  // Fetch all countries and display them initially
  getAllCountries("");
});
</script>

<template>
  <div class="container mx-auto px-4">
    <!-- Input field -->
    <input
      class="w-full max-w-md mx-auto border border-gray-300 mt-2 px-4 py-2 rounded focus:outline-none focus:ring focus:border-blue-500 mb-5 bg-white"
      type="text"
      v-model="name"
      placeholder="Search for a country..."
    />

    <!-- Grid of countries -->
    <div
      class="grid gap-4 mt-4"
      :class="{
        'grid-cols-1': filteredCountries.length === 1,
        'grid-cols-2': filteredCountries.length === 2,
        'grid-cols-3': filteredCountries.length === 3,
        'grid-cols-1': filteredCountries.length > 3,
        'lg:grid-cols-5': filteredCountries.length > 4,
        'md:grid-cols-4': filteredCountries.length > 4, // Show 5 columns on medium screens and above
        'sm:grid-cols-2': filteredCountries.length > 4,
        'xs:grid-cols-1': filteredCountries.length > 4,
        'justify-center': filteredCountries.length < 4, // Center-align when less than 4 items
      }"
    >
      <div
        v-for="country in filteredCountries"
        :key="country.name"
        class="bg-white rounded-lg shadow-md p-4 transition-transform duration-300 transform hover:scale-105 hover:shadow-lg"
      >
        <!-- Country Name -->
        <div class="font-bold text-blue-700 text-xl mb-2">
          {{ country.name }}
        </div>

        <!-- Capital City -->
        <div class="text-gray-600">{{ country.capital }}</div>

        <!-- Flag Icon -->
        <img
          :src="country.flag"
          alt="Country Flag"
          class="mt-4 mx-auto w-12 h-8 object-contain"
        />
      </div>
    </div>
  </div>
</template>

<style>
body {
  margin: 0;
  padding: 0;
  background-color: #f7fafc; /* You can adjust the background color here */
}
</style>

<style scoped></style>
