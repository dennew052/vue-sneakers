<script setup>
import { computed, provide, ref, watch } from 'vue'
import Header from '@/components/Header.vue'
import Drawer from '@/components/Drawer.vue'

const cart = ref([])
const drawerOpen = ref(false)
const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))
const taxPrice = computed(() => Math.round(totalPrice.value * 0.05))

const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}

const addToCart = (item) => {
  cart.value.push(item)
  item.isAdded = true
}

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}

watch(cart, () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  { deep: true }
)

provide('cart', {
  cart,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart
})
</script>

<template>
  <Drawer
    v-if="drawerOpen"
    :total-price="totalPrice"
    :taxPrice="taxPrice"
  />
  <div class="w-full m-auto bg-white rounded-xl shadow-xl mt-14 lg:w-4/5">
    <Header :total-price="totalPrice" @open-drawer="openDrawer" />

    <div class="p-10">
      <router-view></router-view>
    </div>
  </div>
</template>