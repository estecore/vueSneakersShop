<template>
  <header class="flex justify-between items-center border-b-2 border-slate-200 p-8">
    <div class="flex items-center gap-4">
      <img src="/logo.png" alt="logo" class="w-10" />
      <div>
        <h2 class="text-xl font-bold uppercase">Vue Sneakers</h2>
        <p class="text-slate-400">Магазин лучших кроссовок</p>
      </div>
    </div>

    <ul class="flex items-center gap-10 text-gray-500">
      <li class="flex items-center gap-3 hover:text-black cursor-pointer">
        <img src="/cart.svg" alt="cart" />
        <b>1205 руб.</b>
      </li>
      <li class="flex items-center gap-3 hover:text-black cursor-pointer">
        <img src="/heart.svg" alt="heart" />
        <span>Закладки</span>
      </li>
      <li class="flex items-center gap-3 hover:text-black cursor-pointer">
        <img src="/profile.svg" alt="profile" />
        <span>Профиль</span>
      </li>
    </ul>
  </header>
</template>
