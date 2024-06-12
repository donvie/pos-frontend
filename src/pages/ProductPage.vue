<template>
  <q-page padding>
    <q-btn
      label="Add product"
      @click="
        productDetails = {};
        action = 'Add';
        dialogLayout = true;
      "
      icon="add"
      class="q-mb-md"
      color="primary"
    />
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
          <q-page padding>
            <q-card align="center" class="q-mb-md" v-if="action === 'Edit'">
              <q-img
                style="height: auto; width: 350px"
                :src="`http://localhost:1337${productDetails.image}`"
              />
            </q-card>
            <q-uploader
              label="Image"
              @added="handleAdded"
              class="q-mb-md"
              style="width: 100%"
              accept=".jpg, image/*"
              hide-upload-btn
            />

            <!-- <q-file outlined v-model="image" label="Image" class="q-mb-md" /> -->
            <q-input
              outlined
              class="q-mb-md"
              v-model="productDetails.productCode"
              ref="barcodeInput"
              label="Product Code"
            >
              <template v-slot:after>
                <q-btn @click="barcodeInput.focus()" round dense flat icon="qr_code_scanner" />
              </template>
            </q-input>
            <q-input
              outlined
              class="q-mb-md"
              v-model="productDetails.productName"
              label="Product Name"
            />
            <q-input
              outlined
              class="q-mb-md"
              v-model="productDetails.productDescription"
              label="Product Description"
            />
            <q-input outlined label="Product Expiry"  v-model="productDetails.dateExpiry" mask="date" :rules="['date']">
              <template v-slot:append>
                <q-icon name="event" class="cursor-pointer">
                  <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                    <q-date :options="enableFutureDates" v-model="productDetails.dateExpiry">
                      <div class="row items-center justify-end">
                        <q-btn v-close-popup label="Close" color="primary" flat />
                      </div>
                    </q-date>
                  </q-popup-proxy>
                </q-icon>
              </template>
            </q-input>
            <q-select
              outlined
              v-model="productDetails.category"
              label="Category"
              :options="[
                'Cat Food',
                'Dog Food',
                'Pet Medicine',
                'Pet Accessories',
                'Services',
              ]"
              class="q-mb-md"
            />
            <q-input
              outlined
              v-model="productDetails.quantity"
              label="Quantity"
              class="q-mb-md"
              type="number"
            />
            <q-input
              type="number"
              outlined
              v-model="productDetails.price"
              label="Price"
              class="q-mb-md"
            />
          </q-page>
        </q-page-container>
      </q-layout>
    </q-dialog>
  </q-page>
</template>

<script setup>
import { onMounted, ref, getCurrentInstance } from "vue";

import { date, useQuasar } from "quasar";

defineOptions({
  name: "PosPage",
});

const $q = useQuasar();
const quasarDate = date;
const { $api } = getCurrentInstance().appContext.config.globalProperties;
const products = ref([]);
const filter = ref("");
const image = ref(null);
const barcodeInput = ref(null);

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
  dateExpiry: "",
  category: "",
  quantity: "",
  price: "",
});

onMounted(() => {
  $api
    .get("/products?pagination[limit]=5000&sort=updatedAt:desc")
    .then((response) => {
      console.log(response.data.data);
      products.value = response.data.data;
    })
    .catch((error) => {
      console.log("error");
    });
});

async function handleAdded(files) {
  image.value = files[0];
}

const enableFutureDates = (date) => {
  const timeStamp = Date.now();
  return date >= quasarDate.formatDate(timeStamp, "YYYY/MM/DD");
};

const onRowClick = (row) => {};

const addProduct = async (row) => {
  let formData = new FormData();
  formData.append("files", image.value);

  const uploadResponse = await $api.post("/upload", formData, {
    headers: {
      "Content-Type": "multipart/form-data",
    },
  });

  console.log("uploadResponse", uploadResponse);
  const uploadedFile = uploadResponse.data[0];
  const fileUrl = uploadedFile.url;

  productDetails.value.image = fileUrl;
  const payload = {
    data: productDetails.value,
  };

  const response = await $api.post("/products", payload);
  console.log(response.data.data);
  products.value.unshift(response.data.data);
  dialogLayout.value = false;
  $q.notify({
    type: "positive",
    message: "Success!",
  });
};
const editProduct = async () => {
  let fileUrl = "";
  if (image.value) {
    let formData = new FormData();
    formData.append("files", image.value);

    const uploadResponse = await $api.post("/upload", formData, {
      headers: {
        "Content-Type": "multipart/form-data",
      },
    });

    console.log("uploadResponse", uploadResponse);
    const uploadedFile = uploadResponse.data[0];
    fileUrl = uploadedFile.url;
  }

  if (fileUrl) {
    productDetails.value.image = fileUrl;
  }

  const payload = {
    data: productDetails.value,
  };

  const response = await $api.put(
    `/products/${productDetails.value.id}`,
    payload
  );
  console.log(response);
  dialogLayout.value = false;
  $q.notify({
    type: "positive",
    message: "Success!",
  });
};

const deleteNow = async (row) => {
  const response = await $api.delete(`/products/${row.id}`);
  console.log("response", response);
  const index = products.value.findIndex((product) => product.id === row.id);
  products.value.splice(index, 1);
  $q.notify({
    type: "positive",
    message: "Success!",
  });
};

const deleteProduct = async (row) => {
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
      deleteNow(row);
    })
    .onCancel(() => {})
    .onDismiss(() => {});
};

const submit = () => {
  if ((action.value === 'Add' ? image.value : true)
    && productDetails.value.productCode
    && productDetails.value.productName
    && productDetails.value.productDescription
    && productDetails.value.dateExpiry
    && productDetails.value.category
    && productDetails.value.quantity
    && productDetails.value.price
  ) {
    if (action.value === "Add") {
        addProduct();
    } else if (action.value === "Edit") {
      editProduct();
    }
  } else {
    $q.notify({
      type: "negative",
      message: "All fields are required!",
    });
  }
};
</script>
