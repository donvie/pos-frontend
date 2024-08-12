<template>
  <q-page padding>
    <q-table
      wrap-cells
      :filter="filter"
      flat
      bordered
      title="Orders List"
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
        <q-tr :props="props" :key="`m_${props.row.index}`">
          <q-td key="image" :props="props">
            {{ props.row.user.firstName }}
          </q-td>
          <q-td key="productCode" :props="props">
            {{ props.row.user.middleName }}
          </q-td>
          <q-td key="productName" :props="props">
            {{ props.row.user.lastName }}
          </q-td>
          <q-td key="quantity" :props="props">
            {{ props.row.user.email }}
          </q-td>
          <q-td key="price" :props="props">
            {{ props.row.user.phoneNumber }}
          </q-td>
          <q-td key="category" :props="props">
            {{
              quasarDate.formatDate(
                props.row.user.createdAt,
                "YYYY/MM/DD HH:mm a"
              )
            }}
          </q-td>
          <q-td key="_id" :props="props" style="width: 250px">
            <!-- {{ props.row.status }} <br /> -->

            <q-select
              dense
              :readonly="user.type === 'user'"
              @update:model-value="proceedToSaveSale(props.row)"
              outlined
              :options="['Purchased']"
              label="Status"
              v-model="props.row.status"
            />
            <!-- <q-btn
              class="q-mt-xs"
              v-if="props.row.status === 'Pending'"
              label="Tag as purchase"
              @click="proceedToSaveSale(props.row)"
            /> -->
          </q-td>
        </q-tr>

        <q-tr
          :props="props"
          :key="`e_${props.row.index}`"
          class="q-virtual-scroll--with-prev"
        >
          <q-td colspan="100%">
            <q-table
              wrap-cells
              flat
              bordered
              :rows="props.row.orders"
              :hide-pagination="true"
              :columns="columns1"
              row-key="productCode"
            >
              <template v-slot:body="props">
                <q-tr :props="props">
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
                  <q-td key="category" :props="props">
                    {{ props.row.category }}
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
          </q-td>
        </q-tr>
        <q-tr
          :props="props"
          :key="`e_${props.row.index}`"
          class="q-virtual-scroll--with-prev"
        >
          <q-td colspan="7" key="image" :props="props">
            <div class="text-h6" align="right">
              Total Price:
              {{
                props.row.orders
                  .reduce((a, b) => a + b.price * b.buy_quantity, 0)
                  .toFixed(2)
              }}
            </div>
          </q-td>
        </q-tr>
      </template>
    </q-table>
  </q-page>
</template>

<script setup>
import { onMounted, ref, getCurrentInstance } from "vue";

import { date, useQuasar } from "quasar";

defineOptions({
  name: "OrderPage",
});

const $q = useQuasar();
const quasarDate = date;
const { $api } = getCurrentInstance().appContext.config.globalProperties;
const products = ref([]);
const filter = ref("");
const status = ref(null);
let user = $q.localStorage.getItem("user");

const columns = [
  { name: "image", label: "First Name", field: "image", align: "left" },
  {
    name: "productCode",
    required: true,
    label: "Middle Name",
    sortable: true,
    align: "left",
  },
  {
    name: "productName",
    align: "productName",
    label: "Last Name",
    field: "productName",
    sortable: true,
    align: "left",
  },
  { name: "quantity", label: "Email", field: "quantity", align: "left" },
  { name: "price", label: "Phone Number", field: "price", align: "left" },
  { name: "category", label: "Date", field: "category", align: "left" },
  { name: "_id", label: "Status", field: "_id", align: "center" },
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
  { name: "category", label: "Category", field: "category", align: "left" },
  { name: "quantity", label: "Quantity", field: "quantity", align: "left" },
  { name: "price", label: "Price", field: "price", align: "left" },
];

onMounted(() => {
  let url = "";
  if (user.type === "user") {
    url = `/orders?&filters[user][id][$eq]=${user.id}&pagination[limit]=5000&sort=updatedAt:desc&populate=*`;
  } else {
    url = `/orders?pagination[limit]=5000&sort=updatedAt:desc&populate=*`;
  }

  $api
    .get(url)
    .then((response) => {
      console.log("ordersorders", response.data.data);
      products.value = response.data.data;
    })
    .catch((error) => {
      console.log("error");
    });
});

const proceedToSaveSale = async (prop) => {
  $q.dialog({
    title: "Confirm",
    message: "Are you sure you want to proceed?",
    ok: {
      unelevated: true,
      color: "primary",
    },
    cancel: {
      flat: true,
      color: "primary",
    },
    persistent: true,
  })
    .onOk(() => {
      const payload = {
        data: {
          status: "Purchased",
        },
      };

      const response = $api.put(`/orders/${prop.id}`, payload);
      prop.status = "Purchased";
      $q.notify({
        type: "positive",
        message: "Success!",
      });
    })
    .onCancel(() => {
      // console.log('>>>> Cancel')
    })
    .onDismiss(() => {
      // console.log('I am triggered on both OK and Cancel')
    });

  // try {
  //   for (const order of prop.orders) {
  //     const payload = {
  //       data: order,
  //     };

  //     const response = await $api.post("/sales", payload);
  //     await lessProduct(order);
  //     console.log(response);
  //   }
  // } catch (error) {
  //   console.log("error", error);
  // }
};
</script>
