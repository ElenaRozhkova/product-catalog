<template>
    <div class="card">
        <img :src="`/images/${product.image}`" :alt="product.name" class="card-image" />
        <div class="card-content">
            <h2>{{ product.name }}</h2>
            <p class="category">{{ product.category }}</p>
            <p class="price">${{ product.price.toFixed(2) }}</p>
        </div>
    </div>
</template>

<script setup>
defineProps({
    product: Object
})
</script>

<style scoped>
.card {
    border: 1px solid #ddd;
    border-radius: 12px;
    padding: 12px;
    width: 200px;
    text-align: center;
    box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
}

.card-image {
    width: 100%;
    border-radius: 8px;
}

.card-content {
    margin-top: 10px;
}

.category {
    font-size: 0.9rem;
    color: gray;
}

.price {
    font-weight: bold;
    margin-top: 5px;
}
</style>