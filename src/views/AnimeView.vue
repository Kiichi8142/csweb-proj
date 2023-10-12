<script setup>
import { StarIcon, ClockIcon, CalendarIcon, BookmarkIcon } from '@heroicons/vue/20/solid';
import { useRoute } from 'vue-router'
import { ref } from 'vue'
import axios from 'axios';
import { useUserStore } from '../stores/userStore';

const userStore = useUserStore()

const route = useRoute()
const id = route.params.id
const detail = ref()

const baseURL = 'https://api.jikan.moe/v4/'
const loadDetail = async () => {
    const response = await axios.get('anime/' + id + '/full', { baseURL: baseURL })
    detail.value = response.data.data
    console.log(response.data.data)
}

loadDetail()

function addToList() {
    const data = {
        ...detail.value
    }
    userStore.addWatchList(data)
}

</script>

<template>
    <div class="max-w-7xl mx-auto p-6 lg:p-8">
        <div v-if="detail" class="flex flex-col md:flex-row p-0 md:p-6 gap-x-8 rounded-md w-full">
            <div class="h-96 w-auto md:w-72 shrink-0">
                <img class="rounded-lg object-cover h-full w-full shadow-lg" :src="detail.images.webp.large_image_url"
                    alt="">
                <div class="flex gap-x-2 items-center justify-center">
                    <button @click="userStore.addFav(id)" class="group p-2 flex items-center text-neutral-100 rounded-md">
                        <StarIcon class="h-6 w-6 group-hover:text-yellow-400" />
                        <p>Add to favorite</p>
                    </button>
                    <button @click="addToList()" class="group flex items-center text-neutral-100 rounded-md">
                        <BookmarkIcon class="h-6 w-6 group-hover:text-blue-600" />
                        <p>Bookmark</p>
                    </button>
                </div>
            </div>
            <div class="flex flex-col">
                <p class="mt-2 text-2xl lg:text-4xl text-neutral-50 font-medium">{{ detail.title_japanese }}</p>
                <div class="flex flex-col md:flex-row gap-x-2 mt-2">
                    <p class="text-neutral-200 font-medium text-base lg:text-lg flex items-center gap-x-1">
                        <StarIcon class="h-6 w-6" />{{ detail.score }}
                    </p>
                    <p class="text-neutral-200 font-medium text-base lg:text-lg flex items-center gap-x-1">
                        <CalendarIcon class="h-6 w-6" />{{ detail.status }}
                    </p>
                    <p class="text-neutral-200 font-medium text-base lg:text-lg flex items-center gap-x-1">
                        <ClockIcon class="h-6 w-6" />{{ detail.broadcast.day }}, {{ detail.broadcast.time }} (JST)
                    </p>
                </div>
                <div class="flex md:flex-row flex-wrap gap-2 mt-2">
                    <div v-for="(genre, key) in detail.genres" :key="key">
                        <p class="px-4 py-1 font-medium bg-neutral-600 text-neutral-200 rounded-md">{{ genre.name }}</p>
                    </div>
                    <div v-for="(theme, key) in detail.themes" :key="key">
                        <p class="px-4 py-1 font-medium bg-neutral-600 text-neutral-200 rounded-md">{{ theme.name }}</p>
                    </div>
                </div>
                <p class="mt-2 text-neutral-400">{{ detail.synopsis }}</p>
                <p class="text-neutral-600">source myanimelist</p>
            </div>
        </div>
        <div v-if="detail" class="flex w-full p-8 bg-neutral-700 rounded-md mt-4">
            <p class="text-xl font-medium text-neutral-200">More details</p>
        </div>
    </div>
</template>