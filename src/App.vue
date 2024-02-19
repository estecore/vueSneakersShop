<!-- 


 =================== TODO: replace type any and ts-ignore 


 -->

<script setup lang="ts">
import { type Ref, ref, watch, provide, computed } from 'vue'
import axios from 'axios'

// @ts-ignore  because compositions api swears at the lack of export default
import Header from './components/Header.vue'
// @ts-ignore  because compositions api swears at the lack of export default
import Drawer from './components/Drawer.vue'

export interface Item {
  id: number
  imageUrl: string
  title: string
  price: number
  isFavorite: boolean
  isAdded: boolean
  favoriteId: number | null
  item_id: number
}

// ============ Cart
const cart: Ref<Array<Item>> = ref([])

const drawerOpen: Ref<boolean> = ref(false)

const totalPrice = computed(() =>
  cart.value.reduce((acc: number, item: Item) => {
    return acc + item.price
  }, 0)
)

const toggleDrawer: () => void = () => {
  drawerOpen.value = !drawerOpen.value
}

const addToCart = (item: Item) => {
  cart.value.push(item)
  item.isAdded = true
}

const removeFromCart = (item: Item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}

watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  {
    deep: true
  }
)

provide('cart', { cart, toggleDrawer, addToCart, removeFromCart })
// ============ /Cart
</script>

<template>
  <Drawer v-if="drawerOpen" :totalPrice="totalPrice" />
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header :totalPrice="totalPrice" @toggleDrawer="toggleDrawer" />

    <div class="p-10">
      <router-view></router-view>
    </div>
  </div>
</template>
