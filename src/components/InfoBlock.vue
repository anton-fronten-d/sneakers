<script setup>
defineProps({
  imageUrl: String,
  title: String,
  description: String,
})
</script>

<template>
  <div class="flex flex-col items-center text-center w-72 mx-auto">
    <img :src="imageUrl" alt="Info image" height="80" width="80" />
    <h2 class="mt-4 text-2xl font-medium">{{ title }}</h2>
    <p class="text-gray-400 mt-2">{{ description }}</p>
  </div>
</template>
