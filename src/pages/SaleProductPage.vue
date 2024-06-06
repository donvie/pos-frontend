<template>
  <q-page padding>
    <q-table
      wrap-cells
      flat
      bordered
      title="Sold Products"
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
          <!-- <q-td key="_id" :props="props">
            <q-btn label="Add" dense />
          </q-td> -->
        </q-tr>
      </template>
    </q-table>
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
  // { name: "_id", label: "Action", field: "_id", align: "left" },
];

onMounted(() => {
  $api
    .get("/sales")
    .then((response) => {
      console.log(response.data.data);
      products.value = response.data.data;
    })
    .catch((error) => {
      console.log("error");
    });
});

const onRowClick = (row) => {};
</script>
