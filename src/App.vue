<script setup>
import { ref, reactive, watch } from 'vue'
import debounce from './debounce'

const searchTerm = ref('')
const searched = reactive([])
const loading = ref(false)

const findProducts = debounce(async term => {
  loading.value = true
  if (term.length > 0) {
    fetch('https://dummyjson.com/products/search?q=' + term)
      .then(res => res.json())
      .then(data => {
        loading.value = false
        searched.products = data.products
      })
  } else {
    loading.value = false
    searched.products = []
  }
}, 300)

watch(searchTerm, newTerm => findProducts(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <ul v-show="searched && searchTerm" class="list-disc">
      <li v-for="item in searched.products" :key="item.id">{{ item.title }} ({{ item.price }}€)</li>
    </ul>
    <p v-show="loading">Loading...</p>
  </div>
</template>
