<template>
    <h1>Izbrannoe</h1>

    <CardList :items="favorites" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus" />
</template>

<script setup>
import CardList from '@/components/CardList.vue';
import axios from 'axios';
import { inject, onMounted, ref, watch } from 'vue';

const favorites = ref([]);

const { addToCart, removeFromCart, cart } = inject('cart');

const onClickAddPlus = (item) => {
    if (!item.isAdded) {
        addToCart(item);
    } else {
        removeFromCart(item);
    }

    console.log(item)
}

const addToFavorite = async (item) => {
    try {
        if (!item.isFavorite) {
            item.isFavorite = true;

            const obj = {
                sneaker_id: item.id
            }

            const { data } = await axios.post(`https://9814fd025fd5e91d.mokky.dev/favorites`, obj);

            item.favoriteId = data.id;
        } else {
            item.isFavorite = false;

            await axios.delete(`https://9814fd025fd5e91d.mokky.dev/favorites/${item.favoriteId}`);
            item.favoriteId = null;
        }

    } catch (e) {
        console.log(e);
    }
}

onMounted(async () => {
    try {
        const { data } = await axios.get('https://9814fd025fd5e91d.mokky.dev/favorites?_relations=sneakers');

        favorites.value = data.map((obj) => {
            return {
                ...obj.sneaker,
                isFavorite: true,
                favoriteId: obj.id,
                isAdded: obj.isAdded
            }
        });

        favorites.value.reverse(); // отображение от нового до старого

    } catch (e) {
        console.log(e);
    }
});

watch(cart, () => {
    favorites.value = favorites.value.map((item) => ({
        ...item,
        isAdded: false
    }))
})
</script>