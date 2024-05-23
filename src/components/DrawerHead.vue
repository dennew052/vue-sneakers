<script setup>
import { inject } from 'vue'

const { closeDrawer } = inject('cart')
</script>
<template>
  <div class="flex gap-5 mb-8 -mt-6 justify-between items-center">
    <h2 class="text-2xl font-bold">Корзина</h2>
    <img @click="closeDrawer"
         src="/close.svg" alt="Close"
         class="h-12 cursor-pointer relative top-1 -right-4 transition hover:scale-110"
    >
  </div>
</template>