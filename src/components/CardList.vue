<script setup lang="ts">
import Card from './Card.vue'

interface Item {
  id: number
  imageUrl: string
  title: string
  price: number
}

defineProps({
  items: { type: Array<Item>, required: true, default: () => [] }
})

const onClickAdd: () => void = () => {
  alert('Добавить!')
}
</script>

<template>
  <div class="grid grid-cols-4 gap-5">
    <Card
      v-for="item in items"
      :key="item.id"
      :imageUrl="item.imageUrl"
      :title="item.title"
      :price="item.price"
      :isAdded="false"
      :isFavorite="false"
      :onClickAdd="onClickAdd"
    />
  </div>
</template>
