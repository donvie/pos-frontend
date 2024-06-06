<template>
  <q-page padding>
    <q-btn
      label="Add product"
      @click="
        action = 'Add';
        dialogLayout = true;
      "
      icon="add"
      class="q-mb-md"
      color="primary"
    />
    <q-table
      wrap-cells
      flat
      bordered
      title="Products List"
      :rows="products"
      :columns="columns"
      row-key="productCode"
    >
      <template v-slot:body="props">
        <q-tr :props="props" @click="onRowClick(props.row)">
          <q-td key="productCode" :props="props">
            {{ props.row.productCode }}
          </q-td>
          <q-td key="productName" :props="props">
            {{ props.row.productName }}
          </q-td>
          <q-td key="productDescription" :props="props">
            {{ props.row.productDescription }}
          </q-td>
          <q-td key="category" :props="props">
            {{ props.row.category }}
          </q-td>
          <q-td key="quantity" :props="props">
            {{ props.row.quantity }}
          </q-td>
          <q-td key="price" :props="props">
            {{ props.row.price }}
          </q-td>
          <q-td key="_id" :props="props">
            <div class="q-gutter-xs">
              <q-btn
                @click="
                  action = 'Edit';
                  productDetails = props.row;
                  dialogLayout = true;
                "
                label="Edit"
                color="primary"
                icon="edit"
                dense
              />
              <q-btn
                @click="deleteProduct(props.row)"
                label="Delete"
                color="primary"
                icon="delete"
                outline
                dense
              />
            </div>
          </q-td>
        </q-tr>
      </template>
    </q-table>

    <q-dialog v-model="dialogLayout">
      <q-layout view="Lhh lpR fff" container class="bg-white text-dark">
        <q-header class="bg-primary">
          <q-toolbar>
            <q-toolbar-title>{{ action }} Product</q-toolbar-title>
            <q-btn
              flat
              @click="dialogLayout = !dialogLayout"
              label="Close"
              dense
              icon="close"
            />
          </q-toolbar>
        </q-header>

        <q-footer class="bg-white">
          <q-toolbar>
            <q-toolbar-title></q-toolbar-title>
            <q-btn @click="submit()" color="primary" icon="save" label="Save" />
          </q-toolbar>
        </q-footer>

        <q-page-container>
          <q-page padding class="q-col-gutter-md">
            <q-input
              outlined
              v-model="productDetails.productCode"
              label="Product Code"
            />
            <q-input
              outlined
              v-model="productDetails.productName"
              label="Product Name"
            />
            <q-input
              outlined
              v-model="productDetails.productDescription"
              label="Product Description"
            />
            <q-input
              outlined
              v-model="productDetails.category"
              label="Category"
            />
            <q-input
              outlined
              v-model="productDetails.quantity"
              label="Quantity"
            />
            <q-input outlined v-model="productDetails.price" label="Price" />
          </q-page>
        </q-page-container>
      </q-layout>
    </q-dialog>
  </q-page>
</template>

<script setup>
import { onMounted, ref, getCurrentInstance } from "vue";

defineOptions({
  name: "PosPage",
});

const { $api } = getCurrentInstance().appContext.config.globalProperties;
const products = ref([]);

const columns = [
  {
    name: "productCode",
    required: true,
    label: "Product Code",
    sortable: true,
    align: "left",
  },
  {
    name: "productName",
    align: "productName",
    label: "Product Name",
    field: "productName",
    sortable: true,
    align: "left",
  },
  {
    name: "productDescription",
    label: "Product Description",
    field: "productDescription",
    sortable: true,
    align: "left",
  },
  { name: "category", label: "Category", field: "category", align: "left" },
  { name: "quantity", label: "Quantity", field: "quantity", align: "left" },
  { name: "price", label: "Price", field: "price", align: "left" },
  { name: "_id", label: "Action", field: "_id", align: "center" },
];

const dialogLayout = ref(false);
const action = ref("");

const productDetails = ref({
  productCode: "",
  productName: "",
  productDescription: "",
  category: "",
  quantity: "",
  price: "",
});

onMounted(() => {
  $api
    .get("/products")
    .then((response) => {
      console.log(response.data.data);
      products.value = response.data.data;
    })
    .catch((error) => {
      console.log("error");
    });
});

const onRowClick = (row) => {};

const addProduct = async (row) => {
  const payload = {
    data: productDetails.value,
  };

  const response = await $api.post("/products", payload);
  console.log(response.data.data);
  products.value.unshift(response.data.data);
  dialogLayout.value = false;
};
const editProduct = async () => {
  const payload = {
    data: productDetails.value,
  };

  const response = await $api.put(
    `/products/${productDetails.value.id}`,
    payload
  );
  console.log(response);
  dialogLayout.value = false;
};

const deleteProduct = async (row) => {
  const response = await $api.delete(`/products/${row.id}`);
  console.log("response", response);
  const index = products.value.findIndex((product) => product.id === row.id);
  products.value.splice(index, 1);
};

const submit = () => {
  if (action.value === "Add") {
    addProduct();
  } else if (action.value === "Edit") {
    editProduct();
  }
};
</script>
