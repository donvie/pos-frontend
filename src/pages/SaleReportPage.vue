<template>
  <q-page padding>
    <div class="row q-col-gutter-md q-mb-md">
      <div class="col-3">
        <q-input type="date" outlined label="Start date" v-model="startDate" />
      </div>
      <div class="col-3">
        <q-input type="date" outlined label="End date" v-model="endDate" />
      </div>
      <div class="col-2 row items-center">
        <q-btn unelevate color="primary" label="Update report" @click="fetchSaleReport()"  />
      </div>
      <div class="col-4 row items-center">
        <div class="col text-right text-h6">Sales Total: {{salesTotal.toFixed(2)}} <br> Sales Quantity Total: {{salesQtyTotal}}</div>
      </div>
    </div>
    <q-btn color="primary" @click="downloadPDF()" class="q-mb-md" label="Download report" icon="picture_as_pdf" />
    <q-table
      wrap-cells
      flat
      bordered
      title="Sales Report"
      :rows="products"
      :columns="columns"
      row-key="productCode"
      :filter="filter"
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
          <q-td key="buy_quantity" :props="props">
            {{ props.row.buy_quantity }}
          </q-td>
          <q-td key="price" :props="props">
            {{ props.row.price }}
          </q-td>
          <q-td key="quantity" :props="props">
            {{ props.row.price * props.row.buy_quantity }}
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
import pdfMake from "pdfmake/build/pdfmake";
import pdfFonts from "pdfmake/build/vfs_fonts";
pdfMake.vfs = pdfFonts.pdfMake.vfs;

import { onMounted, ref, getCurrentInstance } from "vue";

defineOptions({
  name: "PosPage",
});

const { $api } = getCurrentInstance().appContext.config.globalProperties;
const products = ref([]);
const filter = ref("");
const startDate = ref(null);
const endDate = ref(null);
const salesTotal = ref(0);
const salesQtyTotal = ref(0);

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
  { name: "buy_quantity", label: "Quantity", field: "buy_quantity", align: "left" },
  { name: "price", label: "Price", field: "price", align: "left" },
  { name: "quantity", label: "Total Price", field: "quantity", align: "left" },
  // { name: "_id", label: "Action", field: "_id", align: "left" },
];

onMounted(() => {
  fetchSaleReport()
});

const downloadPDF = () => {
  const tableBody = [
    ['Product Code', 'Product Name', 'Description', 'Category', 'Quantity', 'Price', 'Buy Quantity', 'Total Price']
  ];

  let totalPrice = 0;

  products.value.forEach(product => {
    const productTotalPrice = product.price * product.buy_quantity;
    totalPrice += productTotalPrice;
    tableBody.push([
      product.productCode,
      product.productName,
      product.productDescription,
      product.category,
      product.quantity,
      product.price,
      product.buy_quantity,
      productTotalPrice
    ]);
  });

  const docDefinition = {
    content: [
      { text: 'Sales Report', style: 'header', alignment: 'center' },
      {
        table: {
          headerRows: 1,
          widths: ['auto', 'auto', '*', 'auto', 'auto', 'auto', 'auto', 'auto'],
          body: tableBody
        }
      }
    ],
    styles: {
      header: {
        fontSize: 18,
        bold: true,
        margin: [0, 0, 0, 10]
      },
      subheader: {
        fontSize: 15,
        bold: true,
        margin: [0, 10, 0, 5]
      },
    },
  };

  pdfMake.createPdf(docDefinition).download('product_details.pdf');
}


const fetchSaleReport = () => {
  let filter = ''
  if (startDate.value && endDate.value) {
    filter = `/sales?pagination[limit]=5000&sort=updatedAt:desc&filters[createdAt][$gte]=${startDate.value}&filters[createdAt][$lte]=${endDate.value}`
  } else {
    filter = `/sales?pagination[limit]=5000&sort=updatedAt:desc`
  }

  $api
    .get(filter)
    .then((response) => {
      console.log(response.data.data);
      products.value = response.data.data;
      salesTotal.value = response.data.data.reduce(
        (a, b) => a + (+b.buy_quantity) * b.price,
        0
      );
      salesQtyTotal.value = response.data.data.reduce(
        (a, b) => a + (+b.buy_quantity),
        0
      );
    })
    .catch((error) => {
      console.log("error");
    });
}

const onRowClick = (row) => {};
</script>
