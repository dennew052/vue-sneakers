<script setup>
defineProps({
  totalPrice: Number
})

const emit = defineEmits(['openDrawer'])
</script>

<template>
  <header class="flex justify-between border-b border-slate-200 px-10 py-8 lg:p-10">
    <router-link to="/">
      <div class="flex items-center gap-4 flex-col lg:flex-row text-center lg:text-left">
        <img src="/logo.png" alt="Logo" class="w-10">
        <div>
          <h2 class="text-xl font-bold uppercase">Vue Sneakers</h2>
          <p class="text-slate-400">Магазин лучших кроссовок</p>
        </div>
      </div>
    </router-link>

    <ul class="flex justify-around gap-10 flex-col lg:flex-row lg:items-center">
      <li @click="() => emit('openDrawer')"
          class="flex items-center cursor-pointer gap-3 text-gray-500 hover:text-black">
        <img src="/cart.svg" alt="Cart">
        <b>{{ totalPrice.toLocaleString() }} руб.</b>
      </li>

      <router-link to="/favorites">
        <li class="flex items-center cursor-pointer gap-3 text-gray-500 hover:text-black">
          <img src="/heart.svg" alt="Cart">
          <span>Закладки</span>
        </li>
      </router-link>

      <router-link to="/profile">
        <li class="flex items-center cursor-pointer gap-3 text-gray-500 hover:text-black">
          <img src="/profile.svg" alt="Profile">
          <span>Профиль</span>
        </li>
      </router-link>
    </ul>
  </header>
</template>