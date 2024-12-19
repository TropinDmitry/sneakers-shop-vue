<template>
    <div class="grid grid-cols-4 gap-5" v-auto-animate>
        <Card v-for="item in items" :key="item.id" :imageUrl='item.imageUrl' :title="item.title" :price="item.price"
            :isAdded="item.isAdded" :onClickFavorite="() => emit('addToFavorite', item)" :isFavorite="item.isFavorite"
            :onClickAdd="() => emit('addToCart', item)" />
    </div>
</template>

<script setup>
import Card from './Card.vue';

defineProps({
    items: Array
});

const emit = defineEmits(['addToFavorite', 'addToCart']);

</script>
<style></style>