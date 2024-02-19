<script setup lang="ts">
import axios from 'axios'
import { computed, inject, ref } from 'vue'

import { type Item } from '../App.vue'
// @ts-ignore  because compositions api swears at the lack of export default
import DrawerHead from './DrawerHead.vue'
// @ts-ignore  because compositions api swears at the lack of export default
import CartItemList from './CartItemList.vue'
// @ts-ignore  because compositions api swears at the lack of export default
import InfoBlockVue from './InfoBlock.vue'

const props = defineProps({
  totalPrice: Number
})

const vatPrice = computed(() => props.totalPrice && Math.ceil(props.totalPrice * 0.05))

const isCreatingOrder = ref(false)
const orderId = ref(null)

const { cart, toggleDrawer }: any = inject('cart')

const createOrder = async () => {
  try {
    isCreatingOrder.value = true
    const { data } = await axios.post<Array<Item>>('https://a0fab315ccc8463d.mokky.dev/order', {
      items: cart.value,
      totalPrice: props.totalPrice && props.totalPrice
    })
    cart.value = []

    orderId.value = data.id
    return data
  } catch (error) {
    console.log(error)
    alert('Произошла ошибка при создании заказа(')
  } finally {
    isCreatingOrder.value = false
  }
}

const cartIsEmpty = computed(() => cart.value.length === 0)

const ButtonDesabled = computed(() => isCreatingOrder.value || cartIsEmpty.value)
</script>

<template>
  <div
    @click="toggleDrawer"
    class="fixed top-0 left-0 h-full w-full bg-black opacity-70 z-10"
  ></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
    <DrawerHead />

    <div v-if="!totalPrice || orderId" class="flex h-full items-center">
      <InfoBlockVue
        v-if="!totalPrice && !orderId"
        imageUrl="/package-icon.png"
        title="Корзина пуста"
        description="Добавьте хотя бы одну кроссовку, чтобы сделать заказ"
      />

      <InfoBlockVue
        v-if="orderId"
        imageUrl="/order-success-icon.png"
        title="Заказ оформлен"
        :description="`Ваш заказ №${orderId} скоро будет передан курьерской доставке`"
      />
    </div>

    <div v-else>
      <CartItemList />

      <div class="flex flex-col gap-4 mt-7">
        <div class="flex gap-2">
          <span>Итого:</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ totalPrice }} ₽</b>
        </div>
        <div class="flex gap-2">
          <span>Налог 5%:</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ vatPrice }} ₽</b>
        </div>

        <button
          @click="createOrder"
          :disabled="ButtonDesabled"
          class="mt-4 bg-lime-500 w-full rounded-xl py-3 text-white cursor-pointer transition disabled:bg-slate-300 disabled:cursor-not-allowed hover:bg-lime-600 active:bg-lime-700"
        >
          Оформить заказ
        </button>
      </div>
    </div>
  </div>
</template>
