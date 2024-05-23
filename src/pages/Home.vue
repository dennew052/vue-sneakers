<script setup>
import CardList from '@/components/CardList.vue'
import axios from 'axios'
import debounce from 'lodash.debounce'
import { computed, inject, onMounted, reactive, ref, watch } from 'vue'

const { cart, addToCart, removeFromCart } = inject('cart')

const items = ref([])
const items2 = ref([
  {"id":1,"title":"Мужские Кроссовки Nike Blazer Mid Suede","price":12999,"imageUrl":"/sneakers/sneakers-1.jpg"},{"id":2,"title":"Мужские Кроссовки Nike Air Max 270","price":15600,"imageUrl":"/sneakers/sneakers-2.jpg"},{"id":3,"title":"Мужские Кроссовки Nike Blazer Mid Suede","price":8499,"imageUrl":"/sneakers/sneakers-3.jpg"},{"id":4,"title":"Кроссовки Puma X Aka Boku Future Rider","price":7800,"imageUrl":"/sneakers/sneakers-4.jpg"},{"id":5,"title":"Кроссовки Future Rider","price":9550,"imageUrl":"/sneakers/sneakers-5.jpg"},{"id":6,"title":"Кроссовки Black Edition","price":16999,"imageUrl":"/sneakers/sneakers-6.jpg"},{"id":7,"title":"Кроссовки Orange Boomb Edition","price":7499,"imageUrl":"/sneakers/sneakers-7.jpg"},{"id":8,"title":"Кроссовки Nike Air Max 270","price":15600,"imageUrl":"/sneakers/sneakers-8.jpg"},{"id":9,"title":"Кроссовки Nike Air Force 1","price":5900,"imageUrl":"/sneakers/sneakers-9.jpg"},{"id":10,"title":"Кроссовки Adidas Ultraboost","price":11500,"imageUrl":"/sneakers/sneakers-10.jpg"},{"id":11,"title":"Кроссовки Puma Clyde All-Pro","price":7600,"imageUrl":"/sneakers/sneakers-11.jpg"},{"id":12,"title":"Кроссовки Converse Chuck Taylor All-Star","price":13000,"imageUrl":"/sneakers/sneakers-12.jpg"}
])

const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const onChangeSelect = event => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = debounce(event => {
  filters.searchQuery = event.target.value
}, 500)

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        item_id: item.id
      }
      item.isFavorite = true
      const { data } = await axios.post('https://40fecf6a473b2a0f.mokky.dev/favorites', obj)
      item.favoriteId = data.id
      console.log(data)
    } else {
      item.isFavorite = false
      await axios.delete(`https://40fecf6a473b2a0f.mokky.dev/favorites/${item.favoriteId}`)
      item.favoriteId = null
    }
  } catch (e) {
    console.log(e)
  }
}

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get('https://40fecf6a473b2a0f.mokky.dev/favorites')

    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.item_id === item.id)

      if (!favorite) {
        return item
      }

      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }
    })
    console.log(items.value)
  } catch (e) {
    console.log(e)
  }
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }

    const { data } = await axios.get('https://40fecf6a473b2a0f.mokky.dev/items', {
      params
    })

    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (e) {
    console.log(e)
    items.value = items2.value
  }
}

const refreshCardList = async () => {
  await fetchItems()
  await fetchFavorites()

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: computed(() => cart.value.some((cartItem) => cartItem.id === item.id))
  }))
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  await refreshCardList()
})

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

// watch(filters, fetchItems)
watch(filters, refreshCardList)
</script>

<template>
  <div class="flex justify-between items-center flex-col lg:flex-row lg:items-start">
    <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

    <div class="flex gap-4 flex-col lg:flex-row">
      <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
        <option value="title">По названию</option>
        <option value="price">По цене (сначала дешевле)</option>
        <option value="-price">По цене (сначала дороже)</option>
      </select>

      <div class="relative">
        <img class="absolute left-4 top-3" src="/search.svg" alt="Search">
        <input @input="onChangeSearchInput"
               class="border rounded-md py-2 pl-11 pr-6 outline-none focus:border-gray-400 placeholder:text-base"
               type="text" placeholder="Поиск">
      </div>
    </div>
  </div>

  <div class="mt-10">
    <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus" />
  </div>
</template>