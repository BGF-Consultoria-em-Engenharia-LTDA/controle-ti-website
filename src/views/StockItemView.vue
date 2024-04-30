<script setup lang="ts">
  import type { Status, Condition, StockData } from '@/types/stock';
  import axios from 'axios';
  import InputText from 'primevue/inputtext';
  import FloatLabel from 'primevue/floatlabel';
  import Textarea from 'primevue/textarea';
  import Dropdown from 'primevue/dropdown';
  import { onMounted, ref, watch } from 'vue';
  import { useRoute } from 'vue-router';

  const route = useRoute();
  const id = route.params.id;
  const item = ref<StockData>();

  const statusOptions = ref<Status[]>(["Disponível", "Em uso", "Quebrado"]);
  const conditionOptions = ref<Condition[]>(["Bom", "Defeituoso", "Quebrado"]);
  
    watch(() => id, fetchData, { immediate: true })

  async function fetchData() {
    try {
      const response = await axios.get(`http://localhost:8000/stock/${id}`);
      item.value = response.data;
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  }

  onMounted(() => {
    fetchData();
  });
</script>

<template>
  <main>
    <div class="row mb-3">
      <div class="col-sm-12">
        <FloatLabel >
          <InputText id="item" v-model="item!.item" class="w-100" />
          <label for="item">Equipamento</label>
        </FloatLabel>
      </div>
    </div>
    <div class="row mb-3">
      <div class="col-sm-6">
        <FloatLabel >
          <InputText id="model" v-model="item!.model" class="w-100" />
          <label for="model">Modelo</label>
        </FloatLabel>
      </div>
      <div class="col-sm-6">
        <FloatLabel >
          <InputText id="tag" v-model="item!.tag" class="w-100" />
          <label for="tag">Tag</label>
        </FloatLabel>
      </div>
    </div>
    <div class="row mb-3">
      <div class="col-sm-12">
        <FloatLabel >
          <Textarea id="notes" v-model="item!.notes" class="w-100" autoResize cols="30" style="resize: none"/>
          <label for="notes">Notas</label>
        </FloatLabel>
      </div>
    </div>
    <div class="row mb-3">
      <div class="col-sm-6">
        <Dropdown v-model="item!.status" :options="statusOptions" placeholder="Status" class="w-100">
        </Dropdown>
      </div>
      <div class="col-sm-6">
        <Dropdown v-model="item!.condition" :options="conditionOptions" placeholder="Condição" class="w-100">
        </Dropdown>
      </div>
    </div>
    <div class="row mt-5">
      <div class="col-sm-12">
        <span style="opacity: 0.5">{{ item }}</span>
      </div>
    </div>
  </main>
</template>
