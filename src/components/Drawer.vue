<script setup lang="ts">
import DrawerHead from './DrawerHead.vue'
import CartItemList from './CartItemList.vue'
</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black opacity-70 z-10"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
    <DrawerHead />

    <CartItemList />

    <div class="flex flex-col gap-4 mt-7">
      <div class="flex gap-2">
        <span>Итого:</span>
        <div class="flex-1 border-b border-dashed"></div>
        <b>12000 ₽</b>
      </div>
      <div class="flex gap-2">
        <span>Налог 5%:</span>
        <div class="flex-1 border-b border-dashed"></div>
        <b>600 ₽</b>
      </div>

      <button
        :disabled="false"
        class="mt-4 bg-lime-500 w-full rounded-xl py-3 text-white cursor-pointer transition disabled:bg-slate-300 disabled:cursor-not-allowed hover:bg-lime-600 active:bg-lime-700"
      >
        Оформить заказ
      </button>
    </div>
  </div>
</template>