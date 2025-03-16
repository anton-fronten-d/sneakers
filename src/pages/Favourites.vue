<script setup>
import { onMounted, ref, watch, reactive, provide, computed } from 'vue'
import axios from 'axios'
import CardList from '../components/CardList.vue'
const favourites = ref([])

onMounted(async () => {
  try {
    const { data } = await axios.get('https://16bd3b6d53d94d38.mokky.dev/favourites')
    favourites.value = data.map((obj) => obj.item)
  } catch (err) {
    console.log(err)
  }
})
</script>

<template>
  <div>
    <h1>Мои Закладки</h1>
    <CardList :items="favourites" />
  </div>
</template>
