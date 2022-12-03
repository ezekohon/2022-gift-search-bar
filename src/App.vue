<script setup>
import { ref, watch } from 'vue'

const searchTerm = ref('')
const products = ref([])
const loading = ref(false)

const findProducts = debounce(async term => {
  if (term.length === 0) {
    products.value = []
    return
  }
  else {
    loading.value = true
    const res = await fetch(`https://dummyjson.com/products/search?q=${term}`);
    const obj = await res.json();
    products.value = obj.products;
    loading.value = false
  }
}, 500)

function debounce(cb, delay = 300) {
  let timeout

  return (...args) => {
    clearTimeout(timeout)
    timeout = setTimeout(() => {
      cb(...args)
    }, delay)
  }
}

watch(searchTerm, newTerm => findProducts(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <div class="spinner" v-if="loading"></div>
    <ul class="list-none" v-if="(!loading && searchTerm)">
      <li v-for="(product, index) in products" :key="index">{{ product.title }}</li>
    </ul>
  </div>
</template>

<style>
.spinner {
  border: 4px solid rgba(0, 0, 0, 0.1);
  width: 36px;
  height: 36px;
  border-radius: 50%;
  border-left-color: #09f;

  animation: spin 1s ease infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}
</style>
