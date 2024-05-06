<script setup lang="ts">
  import type { Status, StockData } from '@/types/stock';
  import DataTable from 'primevue/datatable';
  import Column from 'primevue/column';
  import InputText from 'primevue/inputtext';
  import Tag from 'primevue/tag';
  import { FilterMatchMode } from 'primevue/api';
  import type { HintedString } from 'primevue/ts-helpers';
  import 'primeicons/primeicons.css';
  import { RouterLink } from 'vue-router';
  import { ref } from 'vue';

  defineProps<{
    items: Array<StockData>
  }>()

  const filters = ref({
    global: { value: null, matchMode: FilterMatchMode.CONTAINS },
    item: { value: null, matchMode: FilterMatchMode.STARTS_WITH },
    tag: { value: null, matchMode: FilterMatchMode.STARTS_WITH },
    status: { value: null, matchMode: FilterMatchMode.IN },
    condition: { value: null, matchMode: FilterMatchMode.EQUALS }
  });

  const getSeverity = (status: Status): HintedString<'secondary' | 'success' | 'info' | 'warning' | 'danger' | 'contrast'> | undefined => {
    switch (status) {
      case 'Disponível':
        return 'success';

      case 'Em uso':
        return 'warning';

      case 'Quebrado':
        return 'danger';

      default:
        return undefined;
    }
  }
</script>

<template>
  <DataTable resizableColumns columnResizeMode="fit" paginator :rows="10" :value="items" sortField="item" :sortOrder="1" :filters="filters"
   dataKey="id" filterDisplay="row" :globalFilterFields="['item', 'tag', 'status', 'condition']" 
   tableStyle="min-width: 50rem" class="striped-table col-sm-12">
    <template #header>
                <div class="col-sm-12">
                    <IconField iconPosition="left">
                        <InputIcon style="margin-right: 15px">
                            <i class="pi pi-search" />
                        </InputIcon>
                        <InputText v-model="filters['global'].value" placeholder="Pesquisar" class="w-75"/>
                    </IconField>
                </div>
            </template>
            <template #empty> Nenhum equipamento encontrado. </template>
            <template #loading> Carregando equipamentos. Por favor, espere. </template>
      <Column field="item" header="Equipamento" sortable></Column>
      <Column field="tag" header="Tag" sortable></Column>
      <Column field="status" header="Status" sortable>
        <template #body="itemProps">
          <Tag :value="itemProps.data.status" :severity="getSeverity(itemProps.data.status)" />
        </template>  
      </Column>
      <Column field="affiliation.tag" header="Afiliação" sortable>
        <template #body="itemProps">
          <td v-tooltip.left="{value: itemProps.data.affiliation.name, autoHide: false}">{{ itemProps.data.affiliation.tag }}</td>
        </template>
      </Column>
      <Column header="Ações">
        <template #body="rowData">
          <div>
            <RouterLink :to="{name: 'stockItem', params: { id: rowData.data.row }}">
              <i class="pi pi-info-circle"></i>
            </RouterLink>
          </div>
        </template>
      </Column>
  </DataTable>
</template>

<style scoped>
  .striped-table ::v-deep .p-datatable-tbody > tr:nth-child(odd) {
    background-color: #f6f6f6;
  }
</style>
