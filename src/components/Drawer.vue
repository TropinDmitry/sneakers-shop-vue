<script setup>
import axios from 'axios';
import { computed, inject, ref } from 'vue';
import CartItemsList from './CartItemsList.vue';
import DrawerHeader from './DrawerHeader.vue';
import InfoBlock from './InfoBlock.vue';

const props = defineProps({
    totalPrice: Number,
    vatPrice: Number,
})

const { cart, closeDrawer } = inject('cart');

const isLoadingOrder = ref(false);
const orderId = ref(null);

const createOrder = async () => {
    try {
        isLoadingOrder.value = true;
        const { data } = await axios.post(`https://9814fd025fd5e91d.mokky.dev/orders`, {
            items: cart.value,
            totalPrice: props.totalPrice.value
        })

        cart.value = [];

        orderId.value = data.id;

        //return data;
    } catch (e) {
        console.log(e);
    } finally {
        isLoadingOrder.value = false;
    }
}

const cartIsEmpty = computed(() => cart.value.length === 0);
const cartButtonDisabled = computed(() => isLoadingOrder.value || cartIsEmpty.value);

</script>

<template>
    <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>

    <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
        <DrawerHeader />

        <div v-if="!totalPrice || orderId" class="flex h-full items-center">
            <InfoBlock v-if="orderId" image-url="/order-success-icon.png" title="Заказ оформлен"
                :description="`Ваш заказ №${orderId} оформлен`" />

            <InfoBlock v-if="!totalPrice && !orderId" image-url="/package-icon.png" title="Корзина пустая"
                description="Добавьте товар, чтобы совершить заказ" />
        </div>

        <div v-else class="mt-8">
            <CartItemsList />

            <div class="flex flex-col gap-4 my-8">
                <div class="flex">
                    <span>Итого:</span>
                    <div class="flex-1 border-b border-dashed"></div>
                    <b>{{ totalPrice }} р.</b>
                </div>

                <div class="flex">
                    <span>Налог 5%:</span>
                    <div class="flex-1 border-b border-dashed"></div>
                    <b>{{ vatPrice }} р.</b>
                </div>

                <button :disabled="cartButtonDisabled" @click="createOrder"
                    class="bg-lime-500 w-full rounded-xl py-3 text-white transition hover:bg-lime-600 active:bg-lime-700 disabled:bg-slate-400 cursor-pointer">
                    Оформить
                </button>
            </div>
        </div>

    </div>
</template>