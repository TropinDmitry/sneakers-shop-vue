<template>
    <header class="flex justify-between border-b border-slate-300 px-8 py-8">

        <router-link to="/">
            <div class="flex items-center gap-4">
                <img src="/logo.png" alt="logo" class="w-10" />
                <div>
                    <h2 class="text-2xl font-bold uppercase">Krosovki</h2>
                    <p class="text-slate-500">Sneakers shop</p>
                </div>
            </div>
        </router-link>

        <ul class="flex items-center gap-8">
            <li @click="() => emit('openDrawer')"
                class="flex items-center gap-3 text-zinc-500 cursor-pointer hover:text-black">
                <img src="/cart.svg" alt="cart" />
                <b>{{ totalPrice }} р.</b>
            </li>

            <router-link to="/favorites">
                <li class="flex items-center gap-3 text-zinc-500 cursor-pointer">
                    <img src="/heart.svg" alt="cart" />
                    <span>Избранное</span>
                </li>
            </router-link>

            <li class="flex items-center gap-3 text-zinc-500 cursor-pointer">
                <img src="/profile.svg" alt="cart" />
                <span>Профиль</span>
            </li>
        </ul>
    </header>
</template>

<script setup>

defineProps({
    totalPrice: Number
})

const emit = defineEmits(['openDrawer']);
</script>