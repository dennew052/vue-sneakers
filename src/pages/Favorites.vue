<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'
import CardList from '@/components/CardList.vue'

const favorites = ref([])
const requestCompleted = ref(false)

onMounted(async () => {
  try {
    const { data } = await axios.get('https://40fecf6a473b2a0f.mokky.dev/favorites?_relations=items')
    favorites.value = data.map(obj => obj.item)
  } catch (e) {
    console.log(e)
  } finally {
    requestCompleted.value = true
  }
})
</script>

<template>
  <div>
    <h1 class="text-3xl font-bold mb-8">Мои закладки</h1>
    <CardList :items="favorites" is-favorites />
  </div>

  <div v-if="!favorites.length && requestCompleted">
    <div class="h-96 flex flex-col justify-center text-center">
      <h2 class="text-3xl font-bold mb-8">Закладок нет</h2>
      <p>Добавляйте закладки, чтобы увидеть их здесь</p>
    </div>
  </div>
</template>