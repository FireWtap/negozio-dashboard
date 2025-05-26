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
      <n-button type="primary" @click="showCreateModal = true"
        >Aggiungi nuovo prodotto</n-button
      >
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

    <!-- Create Product Modal -->
    <n-modal
      v-model:show="showCreateModal"
      preset="dialog"
      title="Aggiungi nuovo prodotto"
      style="width: 600px"
      @after-leave="resetForm"
    >
      <template #header>
        <div style="font-size: 18px; font-weight: 600">
          Aggiungi nuovo prodotto
        </div>
      </template>
      <n-form
        ref="formRef"
        :model="newProduct"
        :rules="formRules"
        label-placement="top"
        style="margin-top: 16px"
      >
        <n-grid :cols="2" :x-gap="16">
          <n-grid-item>
            <n-form-item label="Nome del prodotto" path="name">
              <n-input
                v-model:value="newProduct.name"
                placeholder="Inserisci il nome del prodotto"
              />
            </n-form-item>
          </n-grid-item>

          <n-grid-item>
            <n-form-item label="Categoria" path="category">
              <n-select
                v-model:value="newProduct.category"
                placeholder="Seleziona una categoria"
                :options="categoryOptions"
              />
            </n-form-item>
          </n-grid-item>
        </n-grid>

        <n-grid :cols="2" :x-gap="16">
          <n-grid-item>
            <n-form-item label="Prezzo (€)" path="price">
              <n-input-number
                v-model:value="newProduct.price"
                placeholder="0.00"
                :min="0"
                :precision="2"
                style="width: 100%"
              />
            </n-form-item>
          </n-grid-item>

          <n-grid-item>
            <n-form-item label="Quantità in magazzino" path="stock">
              <n-input-number
                v-model:value="newProduct.stock"
                placeholder="0"
                :min="0"
                style="width: 100%"
              />
            </n-form-item>
          </n-grid-item>
        </n-grid>

        <n-form-item label="Taglie disponibili" path="sizes">
          <n-select
            v-model:value="newProduct.sizes"
            placeholder="Seleziona le taglie"
            multiple
            :options="sizeOptions"
          />
        </n-form-item>

        <n-form-item label="URL Immagine" path="image">
          <n-input
            v-model:value="newProduct.image"
            placeholder="https://esempio.com/immagine.jpg (opzionale)"
          />
        </n-form-item>

        <n-form-item label="Descrizione" path="description">
          <n-input
            v-model:value="newProduct.description"
            type="textarea"
            placeholder="Descrizione del prodotto (opzionale)"
            :rows="3"
          />
        </n-form-item>
      </n-form>

      <template #action>
        <div style="display: flex; gap: 12px; justify-content: flex-end">
          <n-button @click="showCreateModal = false">Annulla</n-button>
          <n-button
            type="primary"
            @click="handleCreateProduct"
            :loading="creating"
          >
            Crea prodotto
          </n-button>
        </div>
      </template>
    </n-modal>

    <!-- Edit Product Modal -->
    <n-modal
      v-model:show="showEditModal"
      preset="dialog"
      title="Modifica prodotto"
      style="width: 600px"
      @after-leave="resetForm"
    >
      <template #header>
        <div style="font-size: 18px; font-weight: 600">Modifica prodotto</div>
      </template>
      <n-form
        ref="editFormRef"
        :model="newProduct"
        :rules="formRules"
        label-placement="top"
        style="margin-top: 16px"
      >
        <n-grid :cols="2" :x-gap="16">
          <n-grid-item>
            <n-form-item label="Nome del prodotto" path="name">
              <n-input
                v-model:value="newProduct.name"
                placeholder="Inserisci il nome del prodotto"
              />
            </n-form-item>
          </n-grid-item>

          <n-grid-item>
            <n-form-item label="Categoria" path="category">
              <n-select
                v-model:value="newProduct.category"
                placeholder="Seleziona una categoria"
                :options="categoryOptions"
              />
            </n-form-item>
          </n-grid-item>
        </n-grid>

        <n-grid :cols="2" :x-gap="16">
          <n-grid-item>
            <n-form-item label="Prezzo (€)" path="price">
              <n-input-number
                v-model:value="newProduct.price"
                placeholder="0.00"
                :min="0"
                :precision="2"
                style="width: 100%"
              />
            </n-form-item>
          </n-grid-item>

          <n-grid-item>
            <n-form-item label="Quantità in magazzino" path="stock">
              <n-input-number
                v-model:value="newProduct.stock"
                placeholder="0"
                :min="0"
                style="width: 100%"
              />
            </n-form-item>
          </n-grid-item>
        </n-grid>

        <n-form-item label="Taglie disponibili" path="sizes">
          <n-select
            v-model:value="newProduct.sizes"
            placeholder="Seleziona le taglie"
            multiple
            :options="sizeOptions"
          />
        </n-form-item>

        <n-form-item label="URL Immagine" path="image">
          <n-input
            v-model:value="newProduct.image"
            placeholder="https://esempio.com/immagine.jpg (opzionale)"
          />
        </n-form-item>

        <n-form-item label="Descrizione" path="description">
          <n-input
            v-model:value="newProduct.description"
            type="textarea"
            placeholder="Descrizione del prodotto (opzionale)"
            :rows="3"
          />
        </n-form-item>
      </n-form>

      <template #action>
        <div style="display: flex; gap: 12px; justify-content: flex-end">
          <n-button @click="showEditModal = false">Annulla</n-button>
          <n-button
            type="primary"
            @click="handleUpdateProduct"
            :loading="editing"
          >
            Aggiorna prodotto
          </n-button>
        </div>
      </template>
    </n-modal>

    <!-- Delete Product Modal -->
    <n-modal
      v-model:show="showDeleteModal"
      preset="dialog"
      title="Conferma eliminazione"
      style="width: 450px"
    >
      <template #header>
        <div style="font-size: 18px; font-weight: 600; color: #d03050">
          Conferma eliminazione
        </div>
      </template>

      <div style="margin: 16px 0">
        <p style="margin-bottom: 12px">
          Sei sicuro di voler eliminare questo prodotto?
        </p>
        <div
          v-if="productToDelete"
          style="
            background: #f8f9fa;
            padding: 12px;
            border-radius: 6px;
            border-left: 4px solid #d03050;
          "
        >
          <strong>{{ productToDelete.name }}</strong
          ><br />
          <span style="color: #666; font-size: 14px"
            >{{ productToDelete.category }} - €{{ productToDelete.price }}</span
          >
        </div>
        <p style="margin-top: 12px; color: #d03050; font-size: 14px">
          <strong>Attenzione:</strong> Questa azione non può essere annullata.
        </p>
      </div>

      <template #action>
        <div style="display: flex; gap: 12px; justify-content: flex-end">
          <n-button @click="showDeleteModal = false">Annulla</n-button>
          <n-button
            type="error"
            @click="confirmDeleteProduct"
            :loading="deleting"
          >
            Elimina prodotto
          </n-button>
        </div>
      </template>
    </n-modal>
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
  NModal,
  NForm,
  NFormItem,
  NSelect,
  NInputNumber,
  useMessage,
  NGrid,
  NGridItem,
} from "naive-ui";
import { SearchOutline } from "@vicons/ionicons5";

definePageMeta({
  layout: "default",
});

const message = useMessage();
const searchQuery = ref("");
const loading = ref(false);
const showCreateModal = ref(false);
const showEditModal = ref(false);
const showDeleteModal = ref(false);
const creating = ref(false);
const editing = ref(false);
const deleting = ref(false);
const formRef = ref(null);
const editFormRef = ref(null);
const productToDelete = ref(null);
const productToEdit = ref(null);

const pagination = {
  pageSize: 10,
};

// Form data for new product
const newProduct = ref({
  name: "",
  category: "",
  price: null,
  sizes: [],
  stock: null,
  image: "",
  description: "",
});

// Form validation rules
const formRules = {
  name: {
    required: true,
    message: "Il nome del prodotto è obbligatorio",
    trigger: ["input", "blur"],
  },
  category: {
    required: true,
    message: "La categoria è obbligatoria",
    trigger: ["change", "blur"],
  },
  price: {
    required: true,
    type: "number",
    message: "Il prezzo è obbligatorio",
    trigger: ["input", "blur"],
  },
  sizes: {
    required: true,
    type: "array",
    min: 1,
    message: "Seleziona almeno una taglia",
    trigger: ["change", "blur"],
  },
  stock: {
    required: true,
    type: "number",
    message: "La quantità in magazzino è obbligatoria",
    trigger: ["input", "blur"],
  },
};

// Options for form selects
const categoryOptions = [
  { label: "Abbigliamento", value: "Abbigliamento" },
  { label: "Calzature", value: "Calzature" },
  { label: "Accessori", value: "Accessori" },
  { label: "Borse", value: "Borse" },
  { label: "Gioielli", value: "Gioielli" },
];

const sizeOptions = [
  { label: "XS", value: "XS" },
  { label: "S", value: "S" },
  { label: "M", value: "M" },
  { label: "L", value: "L" },
  { label: "XL", value: "XL" },
  { label: "XXL", value: "XXL" },
  { label: "28", value: "28" },
  { label: "30", value: "30" },
  { label: "32", value: "32" },
  { label: "34", value: "34" },
  { label: "36", value: "36" },
  { label: "38", value: "38" },
  { label: "39", value: "39" },
  { label: "40", value: "40" },
  { label: "41", value: "41" },
  { label: "42", value: "42" },
  { label: "43", value: "43" },
  { label: "44", value: "44" },
  { label: "45", value: "45" },
];

// Handle product creation
const handleCreateProduct = async () => {
  try {
    await formRef.value?.validate();
    creating.value = true;

    // Simulate API call
    await new Promise((resolve) => setTimeout(resolve, 1000));

    // Create new product object
    const productToAdd = {
      id: productsData.value.length + 1,
      name: newProduct.value.name,
      category: newProduct.value.category,
      price: newProduct.value.price,
      sizes: newProduct.value.sizes,
      stock: newProduct.value.stock,
      image:
        newProduct.value.image ||
        `https://via.placeholder.com/50x50/ff6b6b/fff?text=${newProduct.value.name.charAt(
          0
        )}`,
      description: newProduct.value.description,
    };

    // Add to products list
    productsData.value.push(productToAdd);

    // Reset form and close modal
    resetForm();
    showCreateModal.value = false;

    message.success("Prodotto creato con successo!");
  } catch (error) {
    message.error("Errore nella validazione del form");
  } finally {
    creating.value = false;
  }
};

// Reset form data
const resetForm = () => {
  newProduct.value = {
    name: "",
    category: "",
    price: null,
    sizes: [],
    stock: null,
    image: "",
    description: "",
  };
  formRef.value?.restoreValidation();
};

// Handle product editing
const handleEditProduct = (product) => {
  productToEdit.value = product;
  newProduct.value = {
    name: product.name,
    category: product.category,
    price: product.price,
    sizes: [...product.sizes],
    stock: product.stock,
    image: product.image,
    description: product.description || "",
  };
  showEditModal.value = true;
};

// Handle product update
const handleUpdateProduct = async () => {
  try {
    await editFormRef.value?.validate();
    editing.value = true;

    // Simulate API call
    await new Promise((resolve) => setTimeout(resolve, 1000));

    // Find and update the product
    const index = productsData.value.findIndex(
      (p) => p.id === productToEdit.value.id
    );
    if (index !== -1) {
      productsData.value[index] = {
        ...productToEdit.value,
        name: newProduct.value.name,
        category: newProduct.value.category,
        price: newProduct.value.price,
        sizes: newProduct.value.sizes,
        stock: newProduct.value.stock,
        image: newProduct.value.image || productToEdit.value.image,
        description: newProduct.value.description,
      };
    }

    // Reset form and close modal
    resetForm();
    showEditModal.value = false;
    productToEdit.value = null;

    message.success("Prodotto aggiornato con successo!");
  } catch (error) {
    message.error("Errore nella validazione del form");
  } finally {
    editing.value = false;
  }
};

// Handle product deletion
const handleDeleteProduct = (product) => {
  productToDelete.value = product;
  showDeleteModal.value = true;
};

// Confirm product deletion
const confirmDeleteProduct = async () => {
  try {
    deleting.value = true;

    // Simulate API call
    await new Promise((resolve) => setTimeout(resolve, 500));

    // Remove product from list
    const index = productsData.value.findIndex(
      (p) => p.id === productToDelete.value.id
    );
    if (index !== -1) {
      productsData.value.splice(index, 1);
    }

    showDeleteModal.value = false;
    productToDelete.value = null;

    message.success("Prodotto eliminato con successo!");
  } catch (error) {
    message.error("Errore durante l'eliminazione del prodotto");
  } finally {
    deleting.value = false;
  }
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
          {
            size: "small",
            secondary: true,
            onClick: () => handleEditProduct(row),
          },
          { default: () => "Modifica" }
        ),
        h(
          NButton,
          {
            size: "small",
            secondary: true,
            type: "error",
            onClick: () => handleDeleteProduct(row),
          },
          { default: () => "Elimina" }
        ),
      ]),
  },
];

const productsData = ref([
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
]);

const filteredProducts = computed(() => {
  if (!searchQuery.value) {
    return productsData.value;
  }
  return productsData.value.filter(
    (product) =>
      product.name.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      product.category.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});
</script>
