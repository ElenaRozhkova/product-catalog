<template>
  <div class="product__catalog">

    <section class="product__header">
      <!-- Filter Bar -->
      <div class="filter__bar">

        <div class="filter__price">
          <!-- Price Range Filters -->
          <div class="filter__mobil">
            <label for="minPrice">Min Price:</label>
            <input type="number" id="minPrice" v-model.number="minPrice" placeholder="0" min="0" />
          </div>

          <div class="filter__mobil">
            <label for="maxPrice">Max Price:</label>
            <input type="number" id="maxPrice" v-model.number="maxPrice" placeholder="1000" min="0" />
          </div>

        </div>

      </div>
      <div class="header__right">
        <div class="search filter__mobil">
          <!-- Search Bar -->
          <label for="search">Search:</label>
          <input type="text" id="search" v-model="searchQuery" placeholder="Search by name" />
        </div>

        <!-- Sorting Dropdown -->
        <div class="sorting filter__mobil">
          <label for="sort">Sort by:</label>
          <select id="sort" v-model="sortOption">
            <option value="default">Default</option>
            <option value="price-asc">Price: Low to High</option>
            <option value="price-desc">Price: High to Low</option>
            <option value="name-asc">Name: A–Z</option>
            <option value="name-desc">Name: Z–A</option>
          </select>
        </div>
      </div>

      <div class="filter__category">
        <label for="category"></label>
        <div class="category-buttons">
          <button
          :class="{ 'active': selectedCategory === 'All' }"
          role="tab"
          :aria-selected="selectedCategory === 'All'"
          @click="selectedCategory = 'All'"
        >
          All
        </button>
          <button
          v-for="cat in categories"
          :key="cat"
          :class="{ 'active': selectedCategory === cat }"
          role="tab"
          :aria-selected="selectedCategory === cat"
          @click="selectedCategory = cat"
        >
          {{ cat }}
        </button>
        </div>
      </div>

    </section>

    <!-- Products Grid -->
    <section class="card-grid">
      <ProductCard v-for="item in visibleProducts" :key="item.id" :product="item" />
    </section>

    <!-- Load More Button -->
    <div class="load-more-container">
      <button @click="loadMore"   role="tab" v-if="hasMore && !loading" :disabled="loading">
        <span v-if="loading">Loading...</span>
        <span v-else>Load More</span>
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import load_more_count from './lib/constants.js'
import ProductCard from './components/ProductCard.vue'
import products from './lib/data.json'

console.log(load_more_count)
// Filters
const selectedCategory = ref('All')
const minPrice = ref(null)
const maxPrice = ref(null)
const searchQuery = ref('') // Search input
const sortOption = ref('default')
const visibleCount = ref(load_more_count)  // Количество товаров, которые показываются изначально
const loading = ref(false) // Флаг для проверки загрузки
const categories = [...new Set(products.map(p => p.category))]

// Проверка, есть ли еще товары для загрузки
const hasMore = computed(() => {
  return visibleCount.value < filteredProducts.value.length
})

// Применение фильтров
const filteredProducts = computed(() => {
  let result = products.filter(product => {
    const inCategory = selectedCategory.value === 'All' || product.category === selectedCategory.value

    const min = minPrice.value !== null && minPrice.value !== '' ? minPrice.value : -Infinity
    const max = maxPrice.value !== null && maxPrice.value !== '' ? maxPrice.value : Infinity

    const inPriceRange = product.price >= min && product.price <= max

    const matchesSearch = product.name.toLowerCase().includes(searchQuery.value.toLowerCase())

    return inCategory && inPriceRange && matchesSearch
  })

  // Применение сортировки
  switch (sortOption.value) {
    case 'price-asc':
      result.sort((a, b) => a.price - b.price)
      break
    case 'price-desc':
      result.sort((a, b) => b.price - a.price)
      break
    case 'name-asc':
      result.sort((a, b) => a.name.localeCompare(b.name))
      break
    case 'name-desc':
      result.sort((a, b) => b.name.localeCompare(a.name))
      break
  }
  return result
})

// Список товаров, которые должны быть видны
const visibleProducts = computed(() => {
  return filteredProducts.value.slice(0, visibleCount.value)
})

// Метод загрузки новых товаров
const loadMore = () => {
  loading.value = true
  setTimeout(() => {
    visibleCount.value += load_more_count  // Увеличиваем количество отображаемых товаров на 6
    loading.value = false
  }, 500)  // Симуляция задержки для демонстрации
}
</script>

<style scoped>
#app {
  display: flex;
  align-items: center;
  margin: auto;
  padding: 0;
}

.filter__category {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 10px;
}

.category-buttons {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.category-buttons button {
  padding: 8px 16px;
  border: 1px solid #ccc;
  background-color: #f5f5f5;
  border-radius: 5px;
  cursor: pointer;
  font-size: 14px;
  transition: all 0.3s ease;
}

.category-buttons button.active {
  background-color: #28a745;
  color: white;
  border-color: #28a745;
}

.category-buttons button:hover {
  background-color: #28a745;
  color: white;
  border-color: #28a745;
}


.product__catalog {
  max-width: 1150px;
  padding: 20px;
  margin: auto;
}

.product__header {

  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 10px;
  padding: 20px;
  background-color: white;
  z-index: 10;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  margin-bottom: 20px;
}

.header__right {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  gap: 10px;
  flex-wrap: wrap;
}

.filter__bar {
  display: flex;
  justify-content: flex-start;
  align-items: flex-start;
  flex-direction: column;
  gap: 10px;
}

.filter__price {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  flex-direction: row;
  gap: 10px;
  flex-wrap: wrap;
}

.product__header label {
  margin-right: 10px;
}

.filter__category {
  display: flex;
  align-items: center;
  justify-content: flex-start;
}

.card-grid {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  gap: 20px;
}

@media (max-width: 625px) {

  .filter__category,
  .filter__mobil {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    justify-content: center;
    width: 100%;
  }
}

.load-more-container {
  text-align: center;
  margin-top: 20px;
}

.load-more-container button {
  padding: 10px 20px;
  background-color: #28a745;
  /* Зелёный */
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s;
}

.load-more-container button:hover {
  background-color: #218838;
  /* Тёмно-зелёный на hover */
}
</style>
