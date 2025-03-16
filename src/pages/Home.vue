<script setup>
import CardList from '../components/CardList.vue'
import axios from 'axios'
import debounce from 'lodash.debounce'
import { onMounted, ref, watch, reactive, provide, computed, inject } from 'vue'

const { addToCart, removeFromCart, cart } = inject('cart')

const items = ref([])

const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
})

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const onChangeSearchInput = debounce((event) => {
  filters.searchQuery = event.target.value
}, 500)

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const addToFavourite = async (item) => {
  try {
    if (!item.isFavourite) {
      const obj = {
        parentId: item.id,
        item,
      }

      item.isFavourite = true
      const { data } = await axios.post('https://16bd3b6d53d94d38.mokky.dev/favourites', obj)
      item.favouriteId = data.id
    } else {
      item.isFavourite = false
      await axios.delete('https://16bd3b6d53d94d38.mokky.dev/favourites/' + item.favouriteId)
      item.favouriteId = null
    }
  } catch (err) {
    console.log(err)
  }
}

const fetchFavourites = async () => {
  try {
    const { data: favourites } = await axios.get('https://16bd3b6d53d94d38.mokky.dev/favourites')
    items.value = items.value.map((item) => {
      const favourite = favourites.find((favourite) => favourite.parentId === item.id)

      if (!favourite) {
        return item
      }

      return {
        ...item,
        isFavourite: true,
        favouriteId: favourite.id,
      }
    })
  } catch (err) {
    console.log(err)
  }
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy,
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }
    const { data } = await axios.get('https://16bd3b6d53d94d38.mokky.dev/listItems', { params })
    items.value = data.map((obj) => ({
      ...obj,
      isFavourite: false,
      isAdded: false,
    }))
  } catch (err) {
    console.log(err)
  }
}

onMounted(async () => {
  cart.value = JSON.parse(localStorage.getItem('cart') || '[]')

  await fetchItems()
  await fetchFavourites()
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id),
  }))
})

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false,
  }))
})

watch(filters, fetchItems)
</script>

<template>
  <div>
    <div class="flex justify-between items-center">
      <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

      <div class="flex gap-4">
        <select
          @change="onChangeSelect"
          name=""
          id=""
          class="py-2 px-3 border rounded-md outline-none"
        >
          <option value="name">По названию</option>
          <option value="price">По цене (дешевле)</option>
          <option value="-price">По цене (дороже)</option>
        </select>

        <div class="relative">
          <img src="/search.svg" alt="" class="absolute left-4 top-3" />
          <input
            @input="onChangeSearchInput"
            type="text"
            placeholder="Поиск..."
            class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
          />
        </div>
      </div>
    </div>
    <div class="mt-10">
      <CardList :items="items" @addToFavourite="addToFavourite" @add-to-cart="onClickAddPlus" />
    </div>
  </div>
</template>
