<script setup lang="ts">
useHead({
  title: "Список продуктів"
})

const columns = [
  { key: 'title', label: 'Назва', sortable: true },
  { key: 'description', label: 'Опис', sortable: true },
  { key: 'price', label: 'Ціна', sortable: true },
  { key: 'rating', label: 'Оцінка', sortable: true, color: 'ratingColor' },
  { key: 'brand', label: 'Бренд', sortable: true },
  { key: 'category', label: 'Категорія', sortable: true },
  { key: 'thumbnail', label: 'Фото' }
]

const { data } = await useLazyAsyncData<any>('products', () => $fetch('https://dummyjson.com/products'))
const products = data.value.products

const page = ref(1)
const pageCount = 5

const q = ref('')

const filteredRows = computed(() => {
  if (!q.value) return products
  return products.filter((product: any) => {
    return Object.values(product).some((value) =>
      String(value).toLowerCase().includes(q.value.toLowerCase())
    )
  })
})

const rows = computed(() => {
  return filteredRows.value.slice((page.value - 1) * pageCount, page.value * pageCount)
})
</script>

<template>
  <div>
    <div class="flex px-3 py-3.5 border-b border-gray-200 dark:border-gray-700">
      <UInput v-model="q" placeholder="Фільтрувати продукти..." />
    </div>
    <UTable :rows="rows" :columns="columns">
      <template #rating-data="{ row }">
        <span :class="{ 'text-green-600': row.rating >= 4.5, 'text-red-600': row.rating < 4.5 }">{{ row.rating }}</span>
      </template>
      <template #thumbnail-data="{ row }">
        <img :src="row.thumbnail" alt="Фото" width="100" height="100" />
      </template>
    </UTable>
    <div class="flex justify-end px-3 py-3.5 border-t border-gray-200 dark:border-gray-700">
      <UPagination v-model="page" :page-count="Math.ceil(filteredRows.length / pageCount)" :total="filteredRows.length" />
    </div>
  </div>
</template>
