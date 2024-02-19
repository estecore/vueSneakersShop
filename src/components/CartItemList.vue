<script lang="ts" setup>
import { inject, type Ref } from 'vue'

import { type Item } from '../App.vue'
// @ts-ignore  because compositions api swears at the lack of export default
import CartItem from './CartItem.vue'

const { cart, removeFromCart }: { cart: Ref<Item[]>; removeFromCart: () => void } = inject('cart')
</script>

<template>
  <div class="flex flex-col flex-1 gap-4" v-auto-animate>
    <CartItem
      v-for="item in cart"
      :key="item.id"
      :title="item.title"
      :imageUrl="item.imageUrl"
      :price="item.price"
      @onClickRemove="() => removeFromCart(item)"
    />
  </div>
</template>
