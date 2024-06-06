<template>
  <q-page padding>
    <div class="row q-col-gutter-md">
      <div class="col-7">
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
            <q-tr :props="props">
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
                <q-btn
                  @click="onRowClick(props.row)"
                  color="primary"
                  label="Add"
                  icon="add"
                  dense
                />
              </q-td>
            </q-tr>
          </template>
        </q-table>
      </div>
      <div class="col-5">
        <q-markup-table wrap-cells>
          <thead>
            <tr>
              <th colspan="6">
                <div class="text-h6 text-left">Order List</div>
              </th>
            </tr>
            <tr>
              <th class="text-left">Product Code</th>
              <th class="text-right">Product Name</th>
              <!-- <th class="text-right">Product Description</th>
              <th class="text-right">Category</th> -->
              <th class="text-right">Quantity</th>
              <th class="text-right">Price</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(order, index) in orders" :key="index">
              <td class="text-left">{{ order.productCode }}</td>
              <td class="text-right">{{ order.productName }}</td>
              <td class="text-right">{{ order.quantity }}</td>
              <td class="text-right">{{ order.price }}</td>
            </tr>
          </tbody>
        </q-markup-table>

        <q-card class="q-mt-md">
          <q-card-section>
            <div class="text-h6">Total Price: 10000</div>
          </q-card-section>

          <q-card-actions align="between">
            <q-btn @click="saveToSale()" unelevated color="primary" icon="save"
              >Confirm buy</q-btn
            >
            <q-btn color="primary" @click="orders = []" icon="cancel" outline
              >Cancel</q-btn
            >
          </q-card-actions>
        </q-card>
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { onMounted, ref, getCurrentInstance } from "vue";

defineOptions({
  name: "PosPage",
});

const { $api } = getCurrentInstance().appContext.config.globalProperties;
const products = ref([]);
const orders = ref([]);

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
  { name: "_id", label: "Action", field: "_id", align: "left" },
];

onMounted(() => {
  $api
    .get("/products?sort=updatedAt:desc")
    .then((response) => {
      console.log(response.data.data);
      products.value = response.data.data;
    })
    .catch((error) => {
      console.log("error");
    });
});

const onRowClick = (row) => {
  orders.value.push(row);
};

const saveToSale = async () => {
  try {
    for (const order of orders.value) {
      const payload = {
        data: order,
      };

      const response = await $api.post("/sales", payload);
      console.log(response);
    }
  } catch (error) {
    console.log("error", error);
  }
};
</script>
