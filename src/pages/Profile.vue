<script setup lang="ts">
import { onMounted, type Ref, ref } from 'vue'
import axios from 'axios'

import { type Item } from '../App.vue'
// @ts-ignore  because compositions api swears at the lack of export default
import CardList from '../components/CardList.vue'

const orders: Ref<Array<Item>> = ref([])
const order: Ref<Array<Item>> = ref([])

onMounted(async () => {
  try {
    const { data } = await axios.get(`https://a0fab315ccc8463d.mokky.dev/order`)
    orders.value = data.map((obj: any) => obj.items.map((item: any) => item))
  } catch (error) {
    console.log(error)
    alert('Произошла ошибка при получении заказов(')
  }
})
</script>

<template>
  <h2 class="text-3xl font-bold mb-8">Заказы</h2>

  <CardList :items="orders[0]" isFavorites />
</template>
