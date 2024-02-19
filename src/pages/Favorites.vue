<script setup lang="ts">
import { onMounted, type Ref, ref } from 'vue'
import axios from 'axios'

import { type Item } from '../App.vue'
// @ts-ignore  because compositions api swears at the lack of export default
import CardList from '../components/CardList.vue'

const favorites: Ref<Array<Item>> = ref([])

onMounted(async () => {
  try {
    const { data } = await axios.get(
      `https://a0fab315ccc8463d.mokky.dev/favorites?_relations=items`
    )
    console.log(data)
    favorites.value = data.map((obj: any) => obj.item)
  } catch (error) {
    console.log(error)
    alert('Произошла ошибка при получении избранных кроссовок(')
  }
})
</script>

<template>
  <h2 class="text-3xl font-bold mb-8">Избранные</h2>

  <CardList :items="favorites" isFavorites />
</template>
