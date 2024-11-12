<script setup lang="ts">

useHead({
  title: "Список продуктів"
})

const columns = [
  {
    key: 'title',
    label: 'Title',
    sortable: true
  },
  {
    key: 'description',
    label: 'Description',
    sortable: true
  },
  {
    key: 'price',
    label: 'Price',
    sortable: true
  },
  {
    key: 'rating',
    label: 'Rating',
    sortable: true,
    color: 'ratingColor'
  },
  {
    key: 'brand',
    label: 'Brand',
    sortable: true
  },
  {
    key: 'category',
    label: 'Category',
    sortable: true
  },
  {
    key: 'thumbnail',
    label: 'Thumbnail'
  }
]

const { data } = await useLazyAsyncData<any>('products', () => $fetch('https://dummyjson.com/products'))
const products = data.value.products
const page = ref(1)
const pageCount = 5
const rows = computed(() => {
  return filteredRows.value.slice((page.value - 1) * pageCount, (page.value) * pageCount)
})
const q = ref('')
const filteredRows = computed(() => {
  if (!q.value) {
    return products
  }
  return products.filter((product: any) => {
    return Object.values(product).some((value) => {
      return String(value).toLowerCase().includes(q.value.toLowerCase())
    })
  })
})
</script>

<template>
  <div>
    <div class="flex px-3 py-3.5 border-b border-gray-200 dark:border-gray-700">
      <UInput v-model="q" placeholder="Filter product..." />
    </div>
    <UTable :rows="rows"  :columns="columns">
      <template #rating-data="{ row }">
        <span :class="{ 'text-green-600': row.rating >= 4.5, 'text-red-600': row.rating < 4.5 }">{{ row.rating }}</span>
      </template>
      <template #thumbnail-data="{ row }">
        <img :src="row.thumbnail" alt="Thumbnail" width="100" height="100" />
      </template>
    </UTable>
    <div class="flex justify-end px-3 py-3.5 border-t border-gray-200 dark:border-gray-700">
      <UPagination v-model="page" :page-count="pageCount" :total="filteredRows.length" />
    </div>
  </div>
</template>