<template>
  <q-page padding>
    <div class="row q-col-gutter-md">
      <div class="col-3">
        <q-card class="my-card bg-white text-primary">
          <q-card-section>
            <div class="text-h6">Total Products</div>
          </q-card-section>

          <q-card-actions align="between">
            <q-btn flat
              ><span style="font-size: 27px">{{
                totalProductCount
              }}</span></q-btn
            >
          </q-card-actions>
        </q-card>
      </div>
      <div class="col-3">
        <q-card class="my-card bg-white text-primary">
          <q-card-section>
            <div class="text-h6">Low Stock Products</div>
          </q-card-section>

          <q-card-actions align="between">
            <q-btn flat
              ><span style="font-size: 27px">{{ lowStockTotal }}</span></q-btn
            >
          </q-card-actions>
        </q-card>
      </div>
      <div class="col-3">
        <q-card class="my-card bg-white text-primary">
          <q-card-section>
            <div class="text-h6">Out of Stock Product</div>
          </q-card-section>

          <q-card-actions align="between">
            <q-btn flat
              ><span style="font-size: 27px">{{ outOfStockTotal }}</span></q-btn
            >
          </q-card-actions>
        </q-card>
      </div>
      <div class="col-3">
        <q-card class="my-card bg-white text-primary">
          <q-card-section>
            <div class="text-h6">Sold Product</div>
          </q-card-section>

          <q-card-actions align="between">
            <q-btn flat
              ><span style="font-size: 27px">{{
                soldProductTotal
              }}</span></q-btn
            >
          </q-card-actions>
        </q-card>
      </div>
      <div class="col-3">
        <q-card class="my-card bg-white text-primary">
          <q-card-section>
            <div class="text-h6">About to expire</div>
          </q-card-section>

          <q-card-actions align="between">
            <q-btn flat
              ><span style="font-size: 27px">{{
                aboutToExpire
              }}</span></q-btn
            >
          </q-card-actions>
        </q-card>
      </div>
    </div>

    <div class="row q-col-gutter-md q-mt-md">
      <div class="col-6">
        <q-table
          wrap-cells
          :filter="filter"
          flat
          bordered
          title="Products List"
          :rows="products"
          :columns="columns"
          row-key="productCode"
        >
          <template v-slot:top-right>
            <q-input
              borderless
              dense
              debounce="300"
              v-model="filter"
              placeholder="Search"
            >
              <template v-slot:append>
                <q-icon name="search" />
              </template>
            </q-input>
          </template>
          <template v-slot:body="props">
            <q-tr :props="props" @click="onRowClick(props.row)">
              <q-td key="image" :props="props">
                <q-img
                  style="height: auto; width: 54px"
                  :src="`http://localhost:1337${props.row.image}`"
                />
              </q-td>
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
              <q-td key="dateExpiry" :props="props">
                {{ props.row.dateExpiry }}
              </q-td>
              <q-td key="quantity" :props="props">
                {{ props.row.quantity }}
              </q-td>
              <q-td key="price" :props="props">
                {{ props.row.price }}
              </q-td>
            </q-tr>
          </template>
        </q-table>
      </div>
      <div class="col-6">
        <q-table
          :filter="filter1"
          wrap-cells
          flat
          bordered
          title="Sold Products"
          :rows="sales"
          :columns="columns1"
          row-key="productCode"
        >
          <template v-slot:top-right>
            <q-input
              borderless
              dense
              debounce="300"
              v-model="filter1"
              placeholder="Search"
            >
              <template v-slot:append>
                <q-icon name="search" />
              </template>
            </q-input>
          </template>
          <template v-slot:body="props">
            <q-tr :props="props" @click="onRowClick(props.row)">
              <q-td key="image" :props="props">
                <q-img
                  style="height: auto; width: 54px"
                  :src="`http://localhost:1337${props.row.image}`"
                />
              </q-td>
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
              <q-td key="dateExpiry" :props="props">
                {{ props.row.dateExpiry }}
              </q-td>
              <q-td key="buy_quantity" :props="props">
                {{ props.row.buy_quantity }}
              </q-td>
              <q-td key="price" :props="props">
                {{ props.row.price }}
              </q-td>
              <q-td key="quantity" :props="props">
                {{ props.row.price * props.row.buy_quantity }}
              </q-td>
            </q-tr>
          </template>
        </q-table>
      </div>
      <div class="col-6">
        <q-table
          wrap-cells
          :filter="filter"
          flat
          bordered
          title="Products about to expire"
          :rows="productsAboutToExpire"
          :columns="columns"
          row-key="productCode"
        >
          <template v-slot:top-right>
            <q-input
              borderless
              dense
              debounce="300"
              v-model="filter"
              placeholder="Search"
            >
              <template v-slot:append>
                <q-icon name="search" />
              </template>
            </q-input>
          </template>
          <template v-slot:body="props">
            <q-tr :props="props" @click="onRowClick(props.row)">
              <q-td key="image" :props="props">
                <q-img
                  style="height: auto; width: 54px"
                  :src="`http://localhost:1337${props.row.image}`"
                />
              </q-td>
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
              <q-td key="dateExpiry" :props="props">
                {{ props.row.dateExpiry }}
              </q-td>
              <q-td key="quantity" :props="props">
                {{ props.row.quantity }}
              </q-td>
              <q-td key="price" :props="props">
                {{ props.row.price }}
              </q-td>
            </q-tr>
          </template>
        </q-table>
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { date } from 'quasar'
import { onMounted, ref, getCurrentInstance } from "vue";

defineOptions({
  name: "IndexPage",
});

const { $api } = getCurrentInstance().appContext.config.globalProperties;

const totalProductCount = ref(0);
const soldProductTotal = ref(0);
const lowStockTotal = ref(0);
const outOfStockTotal = ref(0);
const aboutToExpire = ref(0);
const sales = ref([]);
const products = ref([]);
const productsAboutToExpire = ref([]);
const filter = ref("");
const filter1 = ref("");

const columns = [
  { name: "image", label: "Image", field: "image", align: "left" },
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
  { name: "dateExpiry", label: "Expiry date", field: "dateExpiry", align: "left" },
  { name: "quantity", label: "Quantity", field: "quantity", align: "left" },
  { name: "price", label: "Price", field: "price", align: "left" },
];

const columns1 = [
  { name: "image", label: "Image", field: "image", align: "left" },
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
  { name: "dateExpiry", label: "Expiry date", field: "dateExpiry", align: "left" },
  { name: "buy_quantity", label: "Quantity", field: "buy_quantity", align: "left" },
  { name: "price", label: "Price", field: "price", align: "left" },
  { name: "quantity", label: "Total Price", field: "quantity", align: "left" },
];

onMounted(() => {

  // Sample array of objects
// const items = [
//   { productCode: "001", productName: "Item 1", createdAt: "2024-06-06T14:27:19.776Z" },
//   { productCode: "002", productName: "Item 2", createdAt: "2024-06-06T14:27:19.776Z" },
//   { productCode: "003", productName: "Item 3", createdAt: "2024-06-06T14:27:19.776Z" },
//   { productCode: "004", productName: "Item 4", createdAt: "2024-06-06T14:27:19.776Z" }
// ];

// // Function to filter items created within the last 5 days
// const filterExpiringItems = (items, daysUntilExpiry = 5) => {
//   const now = new Date();
//   const expiryDate = new Date(now.getTime() - daysUntilExpiry * 24 * 60 * 60 * 1000);

//   return items.filter(item => {
//     const createdAt = new Date(item.createdAt);
//     return createdAt >= expiryDate && createdAt <= now;
//   });
// };

// Filter items created within the last 5 days
// const expiringItems = filterExpiringItems(items);

// console.log('expiringItems', expiringItems);

  $api
    .get("/sales?pagination[limit]=5000&&sort=updatedAt:desc")
    .then((response) => {
      sales.value = response.data.data;
      soldProductTotal.value = response.data.data.reduce(
        (a, b) => a + (+b.buy_quantity),
        0
      );
    })
    .catch((error) => {
      console.log("error");
    });

  $api
    .get("/products?pagination[limit]=5000&sort=updatedAt:desc")
    .then((response) => {
      console.log('responseresponse', response),
      products.value = response.data.data;
      totalProductCount.value = response.data.meta.pagination.total;
      lowStockTotal.value = response.data.data.filter(
        (product) => product.quantity < 5
      ).length;
      outOfStockTotal.value = response.data.data.filter(
        (product) => product.quantity <= 0
      ).length;

      aboutToExpire.value = response.data.data.filter((product) => {
        const products = product.dateExpiry && date.getDateDiff(product.dateExpiry, Date.now(), 'days') <= 5
        return products
      }).length

      productsAboutToExpire.value = response.data.data.filter((product) => {
        const products = product.dateExpiry && date.getDateDiff(product.dateExpiry, Date.now(), 'days') <= 5
        return products
      })

    })
    .catch((error) => {
      console.log("error");
    });
});
</script>
