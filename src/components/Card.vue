<template>
    <div
        class="relative bg-white border border-slate-100 rounded-2xl p-8 cursor-pointer hover:-translate-y-2 hover:shadow-xl transition">

        <img :src="!isFavorite ? '/like-1.svg' : '/like-2.svg'" alt="like" class="absolute top-6 left-6"
            @click="onClickFavorite" />
        <img :src="imageUrl" alt="sneaker" />
        <p>{{ title }}</p>

        <div class="flex justify-between mt-5">
            <div class="flex flex-col">
                <span class="text-slate-400">Цена: </span>
                <b>{{ price }} р.</b>
            </div>

            <img :src="!isAdded ? '/plus.svg' : '/checked.svg'" alt="add" @click="onClickAdd" />
        </div>
    </div>
</template>
<script setup>
defineProps({
    id: Number,
    title: String,
    imageUrl: String,
    price: Number,
    isFavorite: Boolean,
    isAdded: Boolean,
    onClickAdd: Function,
    onClickFavorite: Function
})



</script>
<style></style>