<script setup lang="ts">
import axios from 'axios'
import debounce from 'lodash.debounce'
import { inject, onMounted, reactive, type Ref, ref, watch } from 'vue'

import {type Item} from '../App.vue'
// @ts-ignore  because compositions api swears at the lack of export default
import CardList from '../components/CardList.vue'


interface Param {
  sortBy: string
  title?: string
}

const items: Ref<Array<Item>> = ref([])

const {addToCart, removeFromCart, cart}: any = inject('cart')

const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

const onClickAddPlus = (item: Item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const onChangeSelect: (event: Event) => void = (event) => {
  filters.sortBy = (event.target as HTMLInputElement).value
}
const onChangeSearchInput =  debounce((event) => {
  filters.searchQuery = (event.target as HTMLInputElement).value}, 200)

const addToFavorite = async (item: Item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        item_id: item.id
      }
      item.isFavorite = true
      const { data } = await axios.post<{ favoriteId: number; id: number }>(
        `https://a0fab315ccc8463d.mokky.dev/favorites`,
        obj
      )
      item.favoriteId = data.id
    } else {
      item.isFavorite = false
      await axios.delete(`https://a0fab315ccc8463d.mokky.dev/favorites/${item.favoriteId}`)
      item.favoriteId = null
    }
  } catch (error) {
    console.log(error)
    alert('Произошла ошибка при добавлении в избранное(')
  }
}

const fetchFavorites: () => void = async () => {
  try {
    const { data: favorites } = await axios.get<Array<Item>>(
      `https://a0fab315ccc8463d.mokky.dev/favorites`
    )
    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite: Item) => favorite.item_id === item.id)
      if (!favorite) {
        return item
      }

      return { ...item, isFavorite: true, favoriteId: favorite.id }
    })
  } catch (error) {
    console.log(error)
    alert('Произошла ошибка при получении избранных кроссовок(')
  }
}

const fetchItems: () => void = async () => {
  try {
    const params: Param = {
      sortBy: filters.sortBy
    }
    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }
    const { data } = await axios.get<Array<Item>>(`https://a0fab315ccc8463d.mokky.dev/items`, {
      params
    })
    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (error) {
    console.log(error)
    alert('Произошла ошибка при получении кроссовок(')
  }
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  await fetchItems()
  await fetchFavorites()

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})


watch(filters, fetchItems)

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

</script>


<template>
    <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

        <div class="flex gap-4">
          <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
            <option value="name">По названию</option>
            <option value="price">По цене(дешевле)</option>
            <option value="-price">По цене(дороже)</option>
          </select>
          <div class="relative">
            <img class="absolute left-4 top-3" src=/search.svg alt="search"/>
            <input
            @input="onChangeSearchInput"
              class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
              type="text"
              placeholder="Поиск.."
            />
          </div>
        </div>
      </div>
      <div class="mt-10">
        <CardList :items="items" @addToFavorite="addToFavorite" @addToCart="onClickAddPlus"/>
      </div>
</template>