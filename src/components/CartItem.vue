<script setup lang="ts">
defineProps({
  title: String,
  imageUrl: String,
  price: Number
})
const emit = defineEmits(['onClickRemove'])
</script>

<template>
  <div class="flex items-center w-full border border-slate-100 p-4 rounded-xl gap-4">
    <img class="w-16" :src="imageUrl" :alt="title" />

    <div class="flex flex-col flex-1">
      <p>{{ title }}</p>

      <div class="flex justify-between mt-2">
        <b class="flex-1">{{ price }} руб.</b>
        <img
          @click="emit('onClickRemove')"
          class="opacity-40 cursor-pointer transition hover:opacity-100"
          src="/close.svg"
          alt="delete"
        />
      </div>
    </div>
  </div>
</template>
