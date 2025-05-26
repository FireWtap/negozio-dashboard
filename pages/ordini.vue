<template>
  <div>
    <h1 style="margin-bottom: 24px; font-size: 28px; font-weight: 600">
      Ordini
    </h1>

    <!-- Search and Filter -->
    <div style="display: flex; gap: 16px; margin-bottom: 24px">
      <n-input
        v-model:value="searchQuery"
        placeholder="Cerca ordini"
        style="max-width: 300px"
      >
        <template #prefix>
          <n-icon :component="SearchOutline" />
        </template>
      </n-input>

      <n-select
        v-model:value="statusFilter"
        placeholder="Filtra per stato"
        :options="statusOptions"
        style="width: 200px"
        clearable
      />
    </div>

    <!-- Orders Table -->
    <n-card>
      <n-data-table
        :columns="columns"
        :data="filteredOrders"
        :pagination="pagination"
        :loading="loading"
      />
    </n-card>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import { NCard, NDataTable, NInput, NIcon, NSelect, NTag } from "naive-ui";
import { SearchOutline } from "@vicons/ionicons5";

definePageMeta({
  layout: "default",
});

const searchQuery = ref("");
const statusFilter = ref(null);
const loading = ref(false);

const pagination = {
  pageSize: 10,
};

const statusOptions = [
  { label: "Completato", value: "completato" },
  { label: "In elaborazione", value: "elaborazione" },
  { label: "Spedito", value: "spedito" },
  { label: "Annullato", value: "annullato" },
];

const getStatusType = (status) => {
  switch (status) {
    case "completato":
      return "success";
    case "elaborazione":
      return "warning";
    case "spedito":
      return "info";
    case "annullato":
      return "error";
    default:
      return "default";
  }
};

const columns = [
  {
    title: "ID Ordine",
    key: "id",
    width: 100,
    render: (row) => `#${row.id}`,
  },
  {
    title: "Cliente",
    key: "customer",
    sorter: "default",
  },
  {
    title: "Data",
    key: "date",
    sorter: (row1, row2) => new Date(row1.date) - new Date(row2.date),
  },
  {
    title: "Prodotti",
    key: "items",
    render: (row) => `${row.items} articoli`,
  },
  {
    title: "Totale",
    key: "total",
    render: (row) => `€${row.total.toFixed(2)}`,
    sorter: (row1, row2) => row1.total - row2.total,
  },
  {
    title: "Stato",
    key: "status",
    render: (row) =>
      h(
        NTag,
        {
          type: getStatusType(row.status),
          size: "small",
        },
        {
          default: () =>
            row.status.charAt(0).toUpperCase() + row.status.slice(1),
        }
      ),
  },
  {
    title: "Metodo Pagamento",
    key: "paymentMethod",
  },
];

const ordersData = [
  {
    id: 1001,
    customer: "Mario Rossi",
    date: "2024-01-15",
    items: 3,
    total: 125.5,
    status: "completato",
    paymentMethod: "Carta di Credito",
  },
  {
    id: 1002,
    customer: "Giulia Bianchi",
    date: "2024-01-14",
    items: 1,
    total: 45.0,
    status: "spedito",
    paymentMethod: "PayPal",
  },
  {
    id: 1003,
    customer: "Luca Verdi",
    date: "2024-01-14",
    items: 2,
    total: 89.99,
    status: "elaborazione",
    paymentMethod: "Bonifico",
  },
  {
    id: 1004,
    customer: "Anna Neri",
    date: "2024-01-13",
    items: 4,
    total: 210.0,
    status: "completato",
    paymentMethod: "Carta di Credito",
  },
  {
    id: 1005,
    customer: "Francesco Blu",
    date: "2024-01-13",
    items: 1,
    total: 25.0,
    status: "annullato",
    paymentMethod: "PayPal",
  },
  {
    id: 1006,
    customer: "Sofia Gialli",
    date: "2024-01-12",
    items: 2,
    total: 95.5,
    status: "spedito",
    paymentMethod: "Carta di Credito",
  },
  {
    id: 1007,
    customer: "Marco Viola",
    date: "2024-01-12",
    items: 3,
    total: 156.75,
    status: "completato",
    paymentMethod: "Bonifico",
  },
  {
    id: 1008,
    customer: "Elena Rosa",
    date: "2024-01-11",
    items: 1,
    total: 80.0,
    status: "elaborazione",
    paymentMethod: "PayPal",
  },
  {
    id: 1009,
    customer: "Andrea Grigi",
    date: "2024-01-11",
    items: 5,
    total: 275.25,
    status: "completato",
    paymentMethod: "Carta di Credito",
  },
  {
    id: 1010,
    customer: "Chiara Marroni",
    date: "2024-01-10",
    items: 2,
    total: 120.0,
    status: "spedito",
    paymentMethod: "Bonifico",
  },
];

const filteredOrders = computed(() => {
  let filtered = ordersData;

  // Filter by search query
  if (searchQuery.value) {
    filtered = filtered.filter(
      (order) =>
        order.customer
          .toLowerCase()
          .includes(searchQuery.value.toLowerCase()) ||
        order.id.toString().includes(searchQuery.value)
    );
  }

  // Filter by status
  if (statusFilter.value) {
    filtered = filtered.filter((order) => order.status === statusFilter.value);
  }

  return filtered;
});
</script>
