<script setup>
import DrawerHead from '@/components/DrawerHead.vue'
import InfoBlock from '@/components/InfoBlock.vue'
import CartItemList from '@/components/CartItemList.vue'
import { computed, inject, ref } from 'vue'
import axios from 'axios'

const { cart, closeDrawer } = inject('cart')

const props = defineProps({
  totalPrice: Number,
  taxPrice: Number
})

const isCreating = ref(false)
const orderId = ref(null)
const createOrder = async () => {
  try {
    isCreating.value = true
    const { data } = await axios.post('https://40fecf6a473b2a0f.mokky.dev/orders', {
      items: cart.value,
      totalPrice: props.totalPrice
    })

    cart.value = []

    orderId.value = data.id

    return data
  } catch (e) {
    console.log(e)
  } finally {
    isCreating.value = false
  }
}

const buttonDisabled = computed(() => isCreating.value ? true : !props.totalPrice)
</script>

<template>
  <div @click="closeDrawer" class="fixed top-0 left-0 w-full h-full bg-black z-10 opacity-70"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-10 overflow-y-auto">
    <DrawerHead />
    <div v-if="orderId" class="flex h-full items-center -mt-24">
      <InfoBlock
        title="Заказ оформлен!"
        description="Ваш заказ скоро будет передан службе курьерской доставки"
        image-url="order-success-icon.png"
      />
    </div>
    <div v-else-if="!totalPrice" class="flex h-full items-center -mt-24">
      <InfoBlock
        title="Корзина пустая"
        description="Добавляйте товары, чтобы сделать заказ"
        image-url="package-icon.png"
      />
    </div>
    <div v-else>
      <CartItemList />
      <div class="flex flex-col gap-4 mt-7">
        <div class="flex gap-2">
          <span>Итого:</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ totalPrice.toLocaleString() }} руб.</b>
        </div>

        <div class="flex gap-2">
          <span>Налог 5%:</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ taxPrice.toLocaleString() }} руб.</b>
        </div>

        <button
          :disabled="buttonDisabled"
          @click="createOrder"
          class="mt-4 bg-lime-500 disabled:bg-slate-300 w-full rounded-xl py-3 text-white transition hover:bg-lime-600 active:bg-lime-700 cursor-pointer">
          Оформить заказ
        </button>
      </div>
    </div>
  </div>
</template>
