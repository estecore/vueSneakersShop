<script setup lang="ts">
import { defineProps } from 'vue'
// @ts-ignore  because compositions api swears at the lack of export default
import Card from './Card.vue'
import { type Item } from '../App.vue'

defineProps({
  items: { type: Array<Item>, required: true, default: () => [] }
})

const emit = defineEmits(['addToFavorite', 'addToCart'])
</script>

<template>
  <div class="grid grid-cols-4 gap-5" v-auto-animate>
    <Card
      v-for="item in items"
      :key="item.id"
      :id="item.id"
      :imageUrl="item.imageUrl"
      :title="item.title"
      :price="item.price"
      :isFavorite="item.isFavorite"
      :isAdded="item.isAdded"
      :onClickFavorite="() => emit('addToFavorite', item)"
      :onClickAdd="() => emit('addToCart', item)"
    />
  </div>
</template>
