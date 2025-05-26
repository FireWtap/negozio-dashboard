<template>
  <div>
    <h1 style="margin-bottom: 24px; font-size: 28px; font-weight: 600">
      Dashboard
    </h1>

    <!-- Time Period Tabs -->
    <n-tabs
      v-model:value="selectedPeriod"
      type="segment"
      style="margin-bottom: 24px"
    >
      <n-tab-pane name="oggi" tab="Oggi" />
      <n-tab-pane name="7giorni" tab="Ultimi 7 Giorni" />
      <n-tab-pane name="30giorni" tab="Ultimi 30 Giorni" />
    </n-tabs>

    <!-- Stats Cards -->
    <div class="stats-grid">
      <n-card>
        <div>
          <h3 style="color: #666; font-size: 14px; margin-bottom: 8px">
            Vendite Totali
          </h3>
          <div style="font-size: 24px; font-weight: 600; margin-bottom: 4px">
            €12,500
          </div>
          <div style="color: #52c41a; font-size: 12px">+12%</div>
        </div>
      </n-card>

      <n-card>
        <div>
          <h3 style="color: #666; font-size: 14px; margin-bottom: 8px">
            Ordini
          </h3>
          <div style="font-size: 24px; font-weight: 600; margin-bottom: 4px">
            350
          </div>
          <div style="color: #ff4d4f; font-size: 12px">-5%</div>
        </div>
      </n-card>

      <n-card>
        <div>
          <h3 style="color: #666; font-size: 14px; margin-bottom: 8px">
            Valore Medio Ordine
          </h3>
          <div style="font-size: 24px; font-weight: 600; margin-bottom: 4px">
            €35.71
          </div>
          <div style="color: #52c41a; font-size: 12px">+8%</div>
        </div>
      </n-card>
    </div>

    <!-- Sales Trend Chart -->
    <div class="chart-container">
      <h2 style="margin-bottom: 16px">Tendenze di Vendita</h2>
      <div>
        <h3 style="color: #666; font-size: 14px; margin-bottom: 8px">
          Vendite nel Tempo
        </h3>
        <div style="font-size: 24px; font-weight: 600; margin-bottom: 4px">
          €12,500
        </div>
        <div style="color: #52c41a; font-size: 12px; margin-bottom: 20px">
          Ultimi 7 Giorni +12%
        </div>

        <!-- Simple Chart Placeholder -->
        <div
          style="
            height: 200px;
            background: #f5f5f5;
            border-radius: 8px;
            display: flex;
            align-items: center;
            justify-content: center;
          "
        >
          <svg width="100%" height="100%" viewBox="0 0 800 200">
            <polyline
              points="50,150 150,120 250,140 350,100 450,130 550,80 650,110 750,90"
              fill="none"
              stroke="#1890ff"
              stroke-width="2"
            />
          </svg>
        </div>

        <div
          style="
            display: flex;
            justify-content: space-between;
            margin-top: 10px;
            font-size: 12px;
            color: #666;
          "
        >
          <span>Lun</span>
          <span>Mar</span>
          <span>Mer</span>
          <span>Gio</span>
          <span>Ven</span>
          <span>Sab</span>
          <span>Dom</span>
        </div>
      </div>
    </div>

    <!-- Top Products Table -->
    <div class="products-table">
      <h2 style="margin-bottom: 16px">Prodotti Più Venduti</h2>
      <n-data-table
        :columns="topProductsColumns"
        :data="topProductsData"
        :pagination="false"
      />
    </div>

    <!-- Sales by Category -->
    <div class="chart-container" style="margin-top: 30px">
      <h2 style="margin-bottom: 16px">Vendite per Categoria</h2>
      <div>
        <h3 style="color: #666; font-size: 14px; margin-bottom: 8px">
          Vendite per Categoria
        </h3>
        <div style="font-size: 24px; font-weight: 600; margin-bottom: 4px">
          €12,500
        </div>
        <div style="color: #52c41a; font-size: 12px; margin-bottom: 20px">
          Ultimi 7 Giorni +12%
        </div>

        <!-- Category bars -->
        <div style="space-y: 12px">
          <div
            v-for="category in categoryData"
            :key="category.name"
            style="margin-bottom: 12px"
          >
            <div
              style="
                display: flex;
                justify-content: space-between;
                margin-bottom: 4px;
              "
            >
              <span style="font-size: 14px">{{ category.name }}</span>
              <span style="font-size: 14px; font-weight: 500"
                >€{{ category.value.toLocaleString() }}</span
              >
            </div>
            <div
              style="
                width: 100%;
                height: 8px;
                background: #f0f0f0;
                border-radius: 4px;
              "
            >
              <div
                :style="`width: ${category.percentage}%; height: 100%; background: ${category.color}; border-radius: 4px;`"
              ></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { NCard, NTabs, NTabPane, NDataTable } from "naive-ui";

definePageMeta({
  layout: "default",
});

const selectedPeriod = ref("7giorni");

const topProductsColumns = [
  {
    title: "Prodotto",
    key: "name",
  },
  {
    title: "Quantità Venduta",
    key: "quantity",
  },
  {
    title: "Ricavo",
    key: "revenue",
    render: (row) => `€${row.revenue.toLocaleString()}`,
  },
];

const topProductsData = [
  {
    name: "T-shirt Bianca Classica",
    quantity: 150,
    revenue: 3000,
  },
  {
    name: "Jeans Slim Fit",
    quantity: 120,
    revenue: 4800,
  },
  {
    name: "Vestito Estivo",
    quantity: 100,
    revenue: 2500,
  },
];

const categoryData = [
  {
    name: "Top",
    value: 8500,
    percentage: 85,
    color: "#1890ff",
  },
  {
    name: "Pantaloni",
    value: 3200,
    percentage: 32,
    color: "#52c41a",
  },
  {
    name: "Vestiti",
    value: 800,
    percentage: 8,
    color: "#faad14",
  },
];
</script>
