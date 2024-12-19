<script setup>
import { computed, onMounted, provide, reactive, ref, watch } from 'vue';
import axios from 'axios';
import Header from './components/Header.vue';
import Drawer from './components/Drawer.vue';
import { RouterView } from 'vue-router';


const cart = ref([]);

const drawerOpen = ref(false);

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0));
const vatPrice = computed(() => Math.round(totalPrice.value * 5 / 100));


const openDrawer = () => {
    drawerOpen.value = true;
}

const closeDrawer = () => {
    drawerOpen.value = false;
}

const addToCart = (item) => {
    cart.value.push(item);
    item.isAdded = true;
}

const removeFromCart = (item) => {
    cart.value.splice(cart.value.indexOf(item), 1);
    item.isAdded = false;
}


watch(
    cart,
    () => {
        localStorage.setItem('cart', JSON.stringify(cart.value))
    },
    {
        deep: true
    }
)

provide('cart', {
    cart,
    closeDrawer,
    openDrawer,
    addToCart,
    removeFromCart
})
</script>

<template>

    <Drawer v-if="drawerOpen" :total-price="totalPrice" :vat-price="vatPrice" />

    <div class="w-4/5 bg-white m-auto rounded-xl shadow-xl mt-10">
        <Header @open-drawer="openDrawer" :total-price="totalPrice" />

        <div class="p-10">
            <!-- <Home /> -->
            <RouterView />
        </div>
    </div>
</template>

<style scoped></style>
