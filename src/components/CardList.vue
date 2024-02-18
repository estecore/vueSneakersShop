<script lang="ts" setup>
import Card from './Card.vue'
const onClickAdd: () => void = () => {
  alert('Добавить!')
}
</script>

<template>
  <div class="grid grid-cols-4 gap-5">
    <Card
      imageUrl="/sneakers/sneakers-1.jpg"
      title="Кроссовки Nike Blazer Mid Suede"
      :price="12000"
      :isAdded="false"
      :isFavorite="false"
      :onClickAdd="onClickAdd"
    />
  </div>
</template>
