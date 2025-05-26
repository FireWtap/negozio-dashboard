<template>
  <div>
    <div
      style="
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 24px;
      "
    >
      <h1 style="font-size: 28px; font-weight: 600">Prodotti</h1>
      <n-button type="primary">Aggiungi nuovo prodotto</n-button>
    </div>

    <!-- Search Bar -->
    <div style="margin-bottom: 24px">
      <n-input
        v-model:value="searchQuery"
        placeholder="Cerca prodotti"
        style="max-width: 400px"
      >
        <template #prefix>
          <n-icon :component="SearchOutline" />
        </template>
      </n-input>
    </div>

    <!-- Products Table -->
    <n-card>
      <n-data-table
        :columns="columns"
        :data="filteredProducts"
        :pagination="pagination"
        :loading="loading"
      />
    </n-card>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import {
  NCard,
  NDataTable,
  NButton,
  NInput,
  NIcon,
  NTag,
  NAvatar,
} from "naive-ui";
import { SearchOutline } from "@vicons/ionicons5";

definePageMeta({
  layout: "default",
});

const searchQuery = ref("");
const loading = ref(false);

const pagination = {
  pageSize: 10,
};

const columns = [
  {
    title: "Immagine",
    key: "image",
    width: 80,
    render: (row) =>
      h(NAvatar, {
        size: "medium",
        src: row.image,
        fallbackSrc: "https://via.placeholder.com/50x50/f0f0f0/666?text=IMG",
      }),
  },
  {
    title: "Nome del prodotto",
    key: "name",
    sorter: "default",
  },
  {
    title: "Categoria",
    key: "category",
    render: (row) => h(NTag, { type: "info" }, { default: () => row.category }),
  },
  {
    title: "Prezzo",
    key: "price",
    render: (row) => `€${row.price}`,
    sorter: (row1, row2) => row1.price - row2.price,
  },
  {
    title: "Taglie disponibili",
    key: "sizes",
    render: (row) => row.sizes.join(", "),
  },
  {
    title: "Disponibilità",
    key: "stock",
    sorter: (row1, row2) => row1.stock - row2.stock,
  },
  {
    title: "Azioni",
    key: "actions",
    render: (row) =>
      h("div", { style: "display: flex; gap: 8px;" }, [
        h(
          NButton,
          { size: "small", secondary: true },
          { default: () => "Modifica" }
        ),
        h(
          NButton,
          { size: "small", secondary: true },
          { default: () => "Elimina" }
        ),
      ]),
  },
];

const productsData = [
  {
    id: 1,
    name: "Maglietta casual",
    category: "Abbigliamento",
    price: 25,
    sizes: ["S", "M", "L"],
    stock: 100,
    image: "https://via.placeholder.com/50x50/ffd700/000?text=👕",
  },
  {
    id: 2,
    name: "Jeans slim fit",
    category: "Abbigliamento",
    price: 50,
    sizes: ["30", "32", "34"],
    stock: 75,
    image: "https://via.placeholder.com/50x50/4169e1/fff?text=👖",
  },
  {
    id: 3,
    name: "Scarpe da ginnastica",
    category: "Calzature",
    price: 80,
    sizes: ["40", "41", "42"],
    stock: 50,
    image: "https://via.placeholder.com/50x50/000000/fff?text=👟",
  },
  {
    id: 4,
    name: "Cappotto invernale",
    category: "Abbigliamento",
    price: 120,
    sizes: ["M", "L", "XL"],
    stock: 25,
    image: "https://via.placeholder.com/50x50/8b4513/fff?text=🧥",
  },
  {
    id: 5,
    name: "Camicia elegante",
    category: "Abbigliamento",
    price: 40,
    sizes: ["S", "M", "L"],
    stock: 60,
    image: "https://via.placeholder.com/50x50/87ceeb/000?text=👔",
  },
  {
    id: 6,
    name: "Pantaloni chino",
    category: "Abbigliamento",
    price: 45,
    sizes: ["30", "32", "34"],
    stock: 80,
    image: "https://via.placeholder.com/50x50/daa520/000?text=👖",
  },
  {
    id: 7,
    name: "Stivali in pelle",
    category: "Calzature",
    price: 100,
    sizes: ["41", "42", "43"],
    stock: 40,
    image: "https://via.placeholder.com/50x50/8b4513/fff?text=👢",
  },
  {
    id: 8,
    name: "Giacca leggera",
    category: "Abbigliamento",
    price: 70,
    sizes: ["S", "M", "L"],
    stock: 55,
    image: "https://via.placeholder.com/50x50/90ee90/000?text=🧥",
  },
  {
    id: 9,
    name: "Maglione di lana",
    category: "Abbigliamento",
    price: 60,
    sizes: ["M", "L", "XL"],
    stock: 70,
    image: "https://via.placeholder.com/50x50/dda0dd/000?text=🧶",
  },
  {
    id: 10,
    name: "Sandali estivi",
    category: "Calzature",
    price: 30,
    sizes: ["39", "40", "41"],
    stock: 90,
    image: "https://via.placeholder.com/50x50/ff6347/fff?text=👡",
  },
];

const filteredProducts = computed(() => {
  if (!searchQuery.value) {
    return productsData;
  }
  return productsData.filter(
    (product) =>
      product.name.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      product.category.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});
</script>
