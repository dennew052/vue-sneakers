<script setup>
defineProps({
  id: Number,
  title: String,
  imageUrl: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean,
  onClickFavorite: Function,
  onClickAdd: Function
})
const getImageUrl = (imageUrl) => {
  return import.meta.env.BASE_URL + imageUrl
}
</script>

<template>
  <div
    class="relative bg-white border border-slate-100 rounded-3xl p-8 cursor-pointer transition hover:-translate-y-2 hover:shadow-xl">
    <img
      v-if="onClickFavorite"
      :src="isFavorite ? 'like-2.svg' : 'like-1.svg'"
      alt="Like"
      class="absolute top-8 left-8 hover:scale-110 transition"
      @click="onClickFavorite"
    >

    <img :src="getImageUrl(imageUrl)" alt="Sneaker">
    <p class="mt-2 h-16">{{ title }}</p>

    <div class="flex justify-between mt-5">
      <div class="flex flex-col">
        <span class="text-slate-400">Цена:</span>
        <b>{{ price.toLocaleString() }} руб.</b>
      </div>
      <img v-if="onClickAdd" @click="onClickAdd" :src="isAdded ? 'checked.svg' : 'plus.svg'" alt="Plus"
           class="hover:scale-110 transition">

    </div>
  </div>
</template>