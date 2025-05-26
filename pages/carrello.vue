<template>
  <div>
    <h1 style="margin-bottom: 24px; font-size: 28px; font-weight: 600">
      Carrello
    </h1>

    <div style="display: flex; gap: 24px">
      <!-- Cart Items -->
      <div style="flex: 1">
        <n-card>
          <div
            v-if="cartItems.length === 0"
            style="text-align: center; padding: 40px"
          >
            <n-icon size="48" style="color: #ccc; margin-bottom: 16px">
              <CartOutline />
            </n-icon>
            <p style="color: #999; font-size: 16px">Il carrello è vuoto</p>
          </div>

          <div v-else>
            <div
              v-for="item in cartItems"
              :key="item.id"
              style="
                display: flex;
                justify-content: space-between;
                align-items: center;
                padding: 16px 0;
                border-bottom: 1px solid #f0f0f0;
              "
              :style="{
                borderBottom:
                  item.id === cartItems[cartItems.length - 1].id
                    ? 'none'
                    : '1px solid #f0f0f0',
              }"
            >
              <div style="flex: 1">
                <h3 style="margin: 0; font-size: 16px; font-weight: 500">
                  {{ item.name }}
                </h3>
                <p style="margin: 4px 0 0 0; color: #666; font-size: 14px">
                  {{ item.description }}
                </p>
              </div>

              <div style="display: flex; align-items: center; gap: 16px">
                <div style="display: flex; align-items: center; gap: 8px">
                  <n-button
                    size="small"
                    circle
                    @click="decreaseQuantity(item.id)"
                    :disabled="item.quantity <= 1"
                  >
                    <template #icon>
                      <n-icon><RemoveOutline /></n-icon>
                    </template>
                  </n-button>

                  <span
                    style="
                      min-width: 20px;
                      text-align: center;
                      font-weight: 500;
                    "
                    >{{ item.quantity }}</span
                  >

                  <n-button
                    size="small"
                    circle
                    @click="increaseQuantity(item.id)"
                  >
                    <template #icon>
                      <n-icon><AddOutline /></n-icon>
                    </template>
                  </n-button>
                </div>

                <div style="min-width: 80px; text-align: right">
                  <span style="font-weight: 600; font-size: 16px">{{
                    formatPrice(item.price * item.quantity)
                  }}</span>
                </div>

                <n-button
                  size="small"
                  type="error"
                  circle
                  @click="removeItem(item.id)"
                >
                  <template #icon>
                    <n-icon><TrashOutline /></n-icon>
                  </template>
                </n-button>
              </div>
            </div>
          </div>
        </n-card>
      </div>

      <!-- Cart Summary -->
      <div style="width: 300px">
        <n-card>
          <h2 style="margin: 0 0 20px 0; font-size: 20px; font-weight: 600">
            Riepilogo
          </h2>

          <div
            v-for="item in cartItems"
            :key="item.id"
            style="
              display: flex;
              justify-content: space-between;
              margin-bottom: 12px;
            "
          >
            <span style="color: #666">{{ item.name }}</span>
            <span style="font-weight: 500">{{
              formatPrice(item.price * item.quantity)
            }}</span>
          </div>

          <n-divider style="margin: 20px 0" />

          <div
            style="
              display: flex;
              justify-content: space-between;
              margin-bottom: 24px;
            "
          >
            <span style="font-size: 18px; font-weight: 600">Totale</span>
            <span style="font-size: 18px; font-weight: 600; color: #18a058">{{
              formatPrice(totalAmount)
            }}</span>
          </div>

          <n-button
            type="primary"
            size="large"
            block
            :disabled="cartItems.length === 0"
            @click="finalizeOrder"
          >
            Finalizza Acquisto
          </n-button>

          <n-button
            v-if="cartItems.length > 0"
            size="medium"
            block
            style="margin-top: 12px"
            @click="clearCart"
          >
            Svuota Carrello
          </n-button>
        </n-card>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import { NCard, NButton, NIcon, NDivider } from "naive-ui";
import {
  CartOutline,
  AddOutline,
  RemoveOutline,
  TrashOutline,
} from "@vicons/ionicons5";

definePageMeta({
  layout: "default",
});

// Sample cart data - in a real app this would come from a store/API
const cartItems = ref([
  {
    id: 1,
    name: "Prodotto 1",
    description: "Maglietta",
    price: 20,
    quantity: 1,
  },
  {
    id: 2,
    name: "Prodotto 2",
    description: "Pantaloni",
    price: 30,
    quantity: 1,
  },
  {
    id: 3,
    name: "Prodotto 3",
    description: "Scarpe",
    price: 80,
    quantity: 1,
  },
  {
    id: 4,
    name: "Prodotto 4",
    description: "Giacca",
    price: 120,
    quantity: 1,
  },
  {
    id: 5,
    name: "Prodotto 5",
    description: "Camicia",
    price: 40,
    quantity: 1,
  },
  {
    id: 6,
    name: "Prodotto 6",
    description: "Vestito",
    price: 90,
    quantity: 1,
  },
]);

const totalAmount = computed(() => {
  return cartItems.value.reduce(
    (total, item) => total + item.price * item.quantity,
    0
  );
});

const formatPrice = (price) => {
  return `${price} €`;
};

const increaseQuantity = (itemId) => {
  const item = cartItems.value.find((item) => item.id === itemId);
  if (item) {
    item.quantity++;
  }
};

const decreaseQuantity = (itemId) => {
  const item = cartItems.value.find((item) => item.id === itemId);
  if (item && item.quantity > 1) {
    item.quantity--;
  }
};

const removeItem = (itemId) => {
  const index = cartItems.value.findIndex((item) => item.id === itemId);
  if (index > -1) {
    cartItems.value.splice(index, 1);
  }
};

const clearCart = () => {
  cartItems.value = [];
};

const finalizeOrder = () => {
  // In a real app, this would process the order
  alert(`Ordine finalizzato! Totale: ${formatPrice(totalAmount.value)}`);
  clearCart();
};
</script>
