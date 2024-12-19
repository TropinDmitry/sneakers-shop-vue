<template>
    <div class="flex justify-between items-center">
        <h2 class="font-bold mb-8">All sneakers</h2>

        <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
            <option value="name">По названию</option>
            <option value="price">По возрастанию цены</option>
            <option value="-price">По убыванию цены</option>
        </select>

        <div class="relative">
            <img class="absolute left-3 top-3" src="/search.svg" />
            <input @input="onChangeSearchInpuit"
                class="border rounded-md py-2 pl-10 pr-4 outline-none focus:border-gray-400" placeholder="Поиск..." />
        </div>
    </div>

    <div class="mt-10">
        <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus" />
    </div>
</template>

<script setup>
import axios from 'axios';
import CardList from '@/components/CardList.vue';
import { inject, onMounted, provide, reactive, ref, watch } from 'vue';
import debounce from 'lodash.debounce';

const { addToCart, removeFromCart, cart } = inject('cart');

const items = ref([]);

const filters = reactive({
    sortBy: 'title',
    searchQuery: ''
})

const onChangeSelect = (event) => {
    filters.sortBy = event.target.value;
}

const onChangeSearchInpuit = debounce((event) => {
    filters.searchQuery = event.target.value;
}, 800);

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

const fetchFavorites = async () => {
    try {
        const { data: favorites } = await axios.get(`https://9814fd025fd5e91d.mokky.dev/favorites`);

        items.value = items.value.map((item) => {
            const favorite = favorites.find((favorite) => favorite.sneaker_id === item.id);

            if (!favorite) {
                return item;
            }

            return {
                ...item,
                isFavorite: true,
                favoriteId: favorite.id
            }
        })

        console.log(items);
    } catch (e) {
        console.log(e);
    }
}

const fetchItems = async () => {
    try {
        const params = {
            sortBy: filters.sortBy
        }

        if (filters.searchQuery) {
            params.title = `*${filters.searchQuery}*`;
        }

        const { data } = await axios.get(`https://9814fd025fd5e91d.mokky.dev/sneakers`, {
            params
        });

        items.value = data.map((obj) => ({
            ...obj,
            isAdded: false,
            isFavorite: false,
            favoriteId: null
        }));

        console.log(items);
    } catch (e) {
        console.log(e);
    }
}

onMounted(async () => {
    const localCart = localStorage.getItem('cart');
    cart.value = localCart ? JSON.parse(localCart) : [];

    await fetchItems();
    await fetchFavorites();

    items.value = items.value.map((item) => ({
        ...item,
        isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
    }))
});

provide('cartItems', {
    items
})

watch(filters, fetchItems);

watch(cart, () => {
    items.value = items.value.map((item) => ({
        ...item,
        isAdded: false
    }))
})
</script>