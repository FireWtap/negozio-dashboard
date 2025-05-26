<template>
  <div class="dashboard-layout">
    <!-- Sidebar -->
    <div class="sidebar">
      <n-menu
        :options="menuOptions"
        :value="activeMenu"
        @update:value="handleMenuSelect"
      />
    </div>

    <!-- Main Content -->
    <div class="main-content">
      <slot />
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { NMenu } from "naive-ui";
import {
  HomeOutline,
  ShirtOutline,
  ReceiptOutline,
  CartOutline,
} from "@vicons/ionicons5";

const router = useRouter();
const route = useRoute();

const activeMenu = ref(route.name || "dashboard");

const menuOptions = [
  {
    label: "Dashboard",
    key: "dashboard",
    icon: () => h(HomeOutline),
  },
  {
    label: "Prodotti",
    key: "prodotti",
    icon: () => h(ShirtOutline),
  },
  {
    label: "Ordini",
    key: "ordini",
    icon: () => h(ReceiptOutline),
  },
  {
    label: "Carrello",
    key: "carrello",
    icon: () => h(CartOutline),
  },
];

const handleMenuSelect = (key) => {
  activeMenu.value = key;
  if (key === "dashboard") {
    router.push("/");
  } else {
    router.push(`/${key}`);
  }
};

// Update active menu when route changes
watch(
  () => route.name,
  (newRoute) => {
    activeMenu.value = newRoute || "dashboard";
  }
);
</script>

<style scoped>
.sidebar {
  padding: 20px;
}
</style>
