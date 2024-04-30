<script setup lang="ts">
import axios from 'axios'
import StockTable from '../components/StockTable.vue'
import { onMounted, ref } from 'vue';
import type { StockData } from '@/types/stock';

const stock = ref<Array<StockData>>([]);

async function fetchData() {
  try {
    const response = await axios.get('http://localhost:8000/stock');
    stock.value = response.data;
  } catch (error) {
    console.error('Error fetching data:', error);
  }

  onMounted(() => {
    fetchData();
  });
}

onMounted(() => {
  fetchData();
});

</script>

<template>
  <main>
    <StockTable :items="stock" />
  </main>
</template>
