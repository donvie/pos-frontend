<template>
  <q-page padding>
    <div class="row q-col-gutter-md">
      <div class="col-4">
        <q-card class="my-card bg-white text-primary">
          <q-card-section>
            <div class="text-h4">ADDRESS</div>
          </q-card-section>
          <br />
          <br />
          <br />
          <q-card-section>
            <div class="text-h5">
              118 MotorShop Baliti City of Sanfernando Pampanga
            </div>
          </q-card-section>
          <br />
          <q-card-section>
            <div class="text-h5">
              118 MotorShopPanipuan City of Sanfernando Pampanga
            </div>
          </q-card-section>
          <br />
          <br />
          <br />
          <div
            style="height: 250px"
            class="q-mt-md"
            ref="mapContainer"
            id="map"
          ></div>
        </q-card>
      </div>
      <div class="col-4">
        <q-card class="my-card bg-white text-primary">
          <q-card-section>
            <div class="text-h4">OPEN HOURS</div>
          </q-card-section>
          <br />
          <q-card-section>
            <div class="text-h5">Monday - Tuesday 8 am - 8pm</div>
          </q-card-section>
          <q-card-section>
            <div class="text-h5">Friday 8 am - 6pm</div>
          </q-card-section>
          <q-card-section>
            <div class="text-h5">Saturday 9 am - 4pm</div>
          </q-card-section>
          <q-card-section>
            <div class="text-h5">Sunday Closed</div>
          </q-card-section>
        </q-card>
      </div>
      <div class="col-4">
        <q-card class="my-card bg-white text-primary">
          <q-card-section>
            <div class="text-h4">CUSTOMER SUPPPORT</div>
          </q-card-section>
          <br />
          <q-card-section>
            <div class="text-h5">+63 938302471</div>
          </q-card-section>
          <br />
          <q-card-section>
            <div class="text-h5">+63 938302471</div>
          </q-card-section>
          <q-card-section>
            <div class="text-h5">118MotorShop@email.com</div>
          </q-card-section>
          <br />
          <br />
        </q-card>
      </div>
    </div>
  </q-page>
</template>

<script setup>
import "leaflet/dist/leaflet.css";
import L from "leaflet";
import { onMounted, ref, watchEffect } from "vue";

let map;
const markers = new Map();
const mapContainer = ref(null);

// Define custom icon
const customIcon = new L.Icon({
  iconUrl: "images/marker-icon.png",
  iconRetinaUrl: "images/marker-icon-2x.png",
  shadowUrl: "images/marker-shadow.png",
  iconSize: [25, 41], // size of the icon
  iconAnchor: [12, 41], // point of the icon which will correspond to marker's location
  popupAnchor: [1, -34], // point from which the popup should open relative to the iconAnchor
  shadowSize: [41, 41], // size of the shadow
});

onMounted(() => {
  initializeMap();
});

const initializeMap = () => {
  const philippinesCoordinates = [15.1014211, 120.6197289];
  map = L.map(mapContainer.value).setView(philippinesCoordinates, 9);
  L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png").addTo(map);

  // Add a marker at the coordinates
  const marker = L.marker(philippinesCoordinates, { icon: customIcon }).addTo(
    map
  );
  marker
    .bindPopup("<b>118 MotorShop</b><br>City of Sanfernando, Pampanga.")
    .openPopup();
};
</script>

<style>
#map {
  width: 100%;
  height: 650px;
}
</style>
