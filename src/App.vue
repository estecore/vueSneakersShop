



<!-- =================== TODO: replace type any and ts-ignore -->




<script setup lang="ts">
import { onMounted, type Ref, ref, watch, reactive, provide, computed } from 'vue'
import axios from 'axios'

// @ts-ignore  because compositions api swears at the lack of export default
import Header from './components/Header.vue'
// @ts-ignore  because compositions api swears at the lack of export default
import CardList from './components/CardList.vue'
// @ts-ignore  because compositions api swears at the lack of export default
import Drawer from './components/Drawer.vue'
import { isTemplateMiddle } from 'typescript'

  export interface Item {
    id: number
    imageUrl: string
    title: string
    price: number
    isFavorite: boolean
    isAdded: boolean
    parentId?: number
    favoriteId?: number | null
  }

  interface Param  {
    sortBy: string,
    title?: string
  }

  const items: Ref<Array<Item>> = ref([])
  const cart: Ref<Array<Item>> = ref([])
  const isCreatingOrder = ref(false)

  const drawerOpen: Ref<boolean> = ref(false) 

  const totalPrice = computed(() => cart.value.reduce((acc: number, item: Item) => {
    return acc + item.price
  }, 0))

  const toggleDrawer: () => void = () => {
    drawerOpen.value = !drawerOpen.value
  }

  const cartIsEmpty = computed(() => cart.value.length === 0)

  const cartButtonDesabled = computed(() => isCreatingOrder.value || cartIsEmpty.value)

  const filters= reactive({
    sortBy:'title',
    searchQuery:''
  })

  const addToCart = (item: Item) => {
      cart.value.push(item)
      item.isAdded = true
    }

    const removeFromCart = (item: Item) => {
      cart.value.splice(cart.value.indexOf(item), 1)
      item.isAdded = false
    }

    const createOrder = async () => {
      try {
        isCreatingOrder.value = true
        const {data} = await axios.post<Array<Item>>('https://a0fab315ccc8463d.mokky.dev/order', {
          items: cart.value,
        totalPrice: totalPrice.value
        })
        cart.value = []

        return data
      } catch (error) {
        console.log(error)
        alert('Произошла ошибка при создании заказа(')
      } finally {
        isCreatingOrder.value = false
      }
    }

  const onClickAddPlus = (item: Item) => {
    if (!item.isAdded) {
      addToCart(item)
    } else {
      removeFromCart(item)
    }
  }

  const onChangeSelect: (event: Event) => void = event => {
    filters.sortBy = (event.target as HTMLInputElement).value
  }
  const onChangeSearchInput: (event: Event) => void = event => {
    filters.searchQuery = (event.target as HTMLInputElement).value
  }

  const fetchFavorites: () => void = async () => {
    try {
      const {data: favorites} = await axios.get<Array<Item>>(`https://a0fab315ccc8463d.mokky.dev/favorites`)
      items.value = items.value.map(item => {
        const favorite = favorites.find((favorite: Item) => favorite.parentId === item.id)
        if (!favorite) {
          return item
        }

        return {...item, isFavorite: true, favoriteId: favorite.id}
      })
    } catch (error) {
      console.log(error)
      alert('Произошла ошибка при получении избранных кроссовок(')
    }
  }

  const addToFavorite = async (item: Item) => {
    try {
      if (!item.isFavorite) {
        const obj = {
        parentId: item.id
      } 
      item.isFavorite = true
      const {data} = await axios.post<{parentId: number, id: number}>(`https://a0fab315ccc8463d.mokky.dev/favorites`, obj)
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

  const fetchItems: () => void = async () => {
  try {
    const params:Param = {
      sortBy: filters.sortBy,
    }
    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }
  const {data} = await axios.get<Array<Item>>(`https://a0fab315ccc8463d.mokky.dev/items`,{
    params
  })
  items.value = data.map(obj => ({
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

watch(cart, ()=> {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

watch(cart, ()=>{
  localStorage.setItem('cart', JSON.stringify(cart.value))
}, {
  deep: true
}
  )

provide('cart', {cart,toggleDrawer,addToCart,removeFromCart})
</script>

<template>
  <Drawer v-if="drawerOpen" :totalPrice="totalPrice" @createOrder="createOrder" :cartButtonDesabled="cartButtonDesabled" />
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header :totalPrice="totalPrice" @toggleDrawer="toggleDrawer" />

    <div class="p-10">
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
    </div>
  </div>
</template>
