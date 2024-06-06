<template>
  <q-page padding>
    <div class="row q-col-gutter-md">
      <div class="col-3">
        <q-card class="my-card bg-white text-primary">
          <q-card-section>
            <div class="text-h6">Total Products</div>
          </q-card-section>

          <q-card-actions align="between">
            <q-btn flat><span style="font-size: 27px">100</span></q-btn>
          </q-card-actions>
        </q-card>
      </div>
      <div class="col-3">
        <q-card class="my-card bg-white text-primary">
          <q-card-section>
            <div class="text-h6">Low Stock Products</div>
          </q-card-section>

          <q-card-actions align="between">
            <q-btn flat><span style="font-size: 27px">100</span></q-btn>
          </q-card-actions>
        </q-card>
      </div>
      <div class="col-3">
        <q-card class="my-card bg-white text-primary">
          <q-card-section>
            <div class="text-h6">Out of Stock Product</div>
          </q-card-section>

          <q-card-actions align="between">
            <q-btn flat><span style="font-size: 27px">100</span></q-btn>
          </q-card-actions>
        </q-card>
      </div>
      <div class="col-3">
        <q-card class="my-card bg-white text-primary">
          <q-card-section>
            <div class="text-h6">Sold Product</div>
          </q-card-section>

          <q-card-actions align="between">
            <q-btn flat><span style="font-size: 27px">100</span></q-btn>
          </q-card-actions>
        </q-card>
      </div>
    </div>
  </q-page>
</template>

<script setup>
defineOptions({
  name: "IndexPage",
});
</script>
