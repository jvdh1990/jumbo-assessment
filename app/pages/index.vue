<template>
    <div>
        <h1 class="text-2xl font-bold mb-4">Stores and opening hours</h1>

        <SearchInput v-model="searchQuery" />

        <!-- Results counter-->
        <div v-if="filteredStores.length" class="mb-2 text-base text-gray-400">{{ filteredStores.length }} stores found
        </div>

        <StoreTile v-for="store in paginatedStores" :key="store.id" :store="store" />

        <Pagination :currentPage="currentPage" :totalPages="totalPages" @update:page="currentPage = $event" />
    </div>
</template>

<script setup>
import { ref, computed, watch, nextTick } from 'vue'
import data from '~/assets/store-data.json'

const searchQuery = ref('')
const currentPage = ref(1)
const pageSize = 10

// Filtered results based on search query
const filteredStores = computed(() =>
    data.stores.filter(store =>
        store.name.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
        store.location.address.city.toLowerCase().includes(searchQuery.value.toLowerCase())
    )
)

// Paginated results
const paginatedStores = computed(() => {
    const start = (currentPage.value - 1) * pageSize
    return filteredStores.value.slice(start, start + pageSize)
})

// Number of pages
const totalPages = computed(() =>
    Math.ceil(filteredStores.value.length / pageSize)
)

// Scroll to top
const scrollToTop = () => nextTick(() => window.scrollTo({ top: 0, behavior: 'smooth' }))

// Reset page and scroll on search
watch(searchQuery, () => currentPage.value = 1)
watch(currentPage, scrollToTop)
</script>