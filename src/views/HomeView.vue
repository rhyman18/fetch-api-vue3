<script setup>
import {
  onBeforeMount,
  onBeforeUnmount,
  onBeforeUpdate,
  // onMounted,
  onUnmounted,
  onUpdated,
  ref,
  // watch,
  watchEffect
} from 'vue'
import ProductCard from '@/components/ProductComponent.vue'
import Pagination from '@/components/PaginationComponent.vue'
import LoadComponent from '@/components/LoadComponent.vue'
import ProductFormComponent from '@/components/ProductFormComponent.vue'
import axios from 'axios'

const products = ref([])
const page = ref(1)
const limit = ref(10)
const isLoading = ref(true)

const fetchData = async () => {
  const API_URL = `http://localhost:3000/products?_page=${page.value}&_per_page=${limit.value}`
  try {
    isLoading.value = true
    const response = await axios.get(API_URL)
    products.value = response.data
  } catch (error) {
    console.error(error)
  } finally {
    isLoading.value = false
  }
}

watchEffect(() => {
  fetchData()
})

onBeforeMount(() => {
  console.log('load before mount')
})

// onMounted(async () => {
//   console.log('load has been mounted')
//   try {
//     isLoading.value = true
//     products.value = await axios
//       .get(`http://localhost:3000/products?_page=${page.value}&_per_page=${limit.value}`)
//       .then((res) => res.data)
//   } catch (error) {
//     console.error(error)
//   } finally {
//     isLoading.value = false
//   }
// })

onBeforeUpdate(() => {
  console.log('load before updated')
})

onUpdated(() => {
  console.log('load has been updated')
})

onBeforeUnmount(() => {
  console.log('load before unmount')
})

onUnmounted(() => {
  console.log('load unmount')
})

// watch(page, async () => {
//   try {
//     isLoading.value = true
//     products.value = await axios
//       .get(`http://localhost:3000/products?_page=${page.value}&_per_page=${limit.value}`)
//       .then((res) => res.data)
//   } catch (error) {
//     console.error(error)
//   } finally {
//     isLoading.value = false
//   }
// })

const changePage = (newPage) => {
  if (newPage < 1) return
  if (newPage > products.value.pages) return
  page.value = newPage
}

const createProduct = async (product) => {
  try {
    await axios.post('http://localhost:3000/products', product)
    fetchData()
  } catch (error) {
    console.error(error)
  }
}
</script>

<template>
  <div v-if="isLoading">
    <LoadComponent />
  </div>
  <main v-else>
    <ProductFormComponent @createProduct="createProduct" />
    <div class="product-grid">
      <ProductCard v-for="(product, index) in products.data" :key="index" :product="product" />
    </div>
    <div class="pagination">
      <Pagination :page="page" :totalPages="products.pages" @changePage="changePage" />
    </div>
  </main>
</template>

<style scoped>
.product-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
  width: 80%;
  margin: 0 auto;
}

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
}
</style>
