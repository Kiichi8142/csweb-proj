<script setup>
import AnimeCard from '../components/AnimeCard.vue';

import axios from 'axios';
import { useRouter } from 'vue-router'
import { ref, onMounted } from 'vue';
import { useAnimeStore } from '@/stores/animeStore.js'

const router = useRouter()
const animeStore = useAnimeStore()

const seasons = ref()
const baseURL = 'https://api.jikan.moe/v4/'

const loadSeasons = async () => {
  const response = await axios.get('seasons/now', { baseURL: baseURL })
  seasons.value = response.data
  animeStore.season.current = seasons.value
}

onMounted(() => {
  // load from pinia instead of fetching
  if (animeStore.season.current) {
    seasons.value = animeStore.season.current
  } else {
    loadSeasons()
  }
})

function goToCategory(type) {
  router.push({ name: 'category', params: { type: type } })
}

function getCurrentSeason() {
  const currentDate = new Date();
  const currentMonth = currentDate.getMonth() + 1; // Month is 0-based, so add 1 to get the actual month.

  if (currentMonth >= 3 && currentMonth <= 5) {
    return 'spring';
  } else if (currentMonth >= 6 && currentMonth <= 8) {
    return 'summer';
  } else if (currentMonth >= 9 && currentMonth <= 11) {
    return 'fall';
  } else {
    return 'winter';
  }
}

</script>

<template>
  <div class="max-w-7xl mx-auto p-6 lg:p-8 text-neutral-50">
    <p class="font-bold text-3xl">Collections</p>
    <div class="grid md:grid-cols-2 gap-8 mt-4 max-w-7xl">
      <div @click="router.push({ name: 'seasonal', params: { year: '2023', season: getCurrentSeason() } })"
        class="relative group hover:cursor-pointer h-28 md:h-100 row-span-1 md:row-span-2 transition-all duration-200 bg-black rounded-md">
        <p class="absolute text-neutral-100 font-medium bottom-10 right-5 text-xl z-10 shadow-md">Seasonal</p>
        <p class="absolute text-neutral-300 font-medium bottom-5 right-5 text-sm z-10 shadow-md">View category</p>
        <img src="@/assets/img/mangaCat.jpg"
          class="rounded-md object-cover w-full h-full shadow-inner opacity-80 group-hover:opacity-50 transition-all duration-200">
      </div>
      <div @click="goToCategory('anime')"
        class="relative group hover:cursor-pointer h-28 md:h-48 transition-all duration-200 bg-black rounded-md">
        <p class="absolute text-neutral-100 font-medium bottom-10 right-5 text-xl z-10 shadow-md">TV Series</p>
        <p class="absolute text-neutral-300 font-medium bottom-5 right-5 text-sm z-10 shadow-md">View category</p>
        <img src="@/assets/img/animeCat.jpg"
          class="rounded-md object-cover object-right-top w-full h-full shadow-inner opacity-80 group-hover:opacity-50 transition-all duration-200">
      </div>
      <div @click="router.push({ name: 'upcoming' })"
        class="relative group hover:cursor-pointer h-28 md:h-48 transition-all duration-200 bg-black rounded-md">
        <p class="absolute text-neutral-100 font-medium bottom-10 right-5 text-xl z-10 shadow-md">Upcoming</p>
        <p class="absolute text-neutral-300 font-medium bottom-5 right-5 text-sm z-10 shadow-md">View category</p>
        <img src="@/assets/img/charaCat.jpg"
          class="rounded-md object-cover object-top w-full h-full shadow-inner opacity-80 group-hover:opacity-50 transition-all duration-200">
      </div>
    </div>
    <p class="font-bold text-3xl mt-4">Current Season</p>
    <p class="text-neutral-400 text-base font-medium">TV Series</p>
    <div v-if="seasons" class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 place-content-center mt-4 max-w-7xl">
      <div v-for="(anime, key) in seasons.data.slice(0, 4)" :key="key">
        <AnimeCard :title="anime.title" :img-url="anime.images.webp.large_image_url" :title-english="anime.title_english"
          :id="anime.mal_id" :type="anime.type" />
      </div>
    </div>
  </div>
</template>
