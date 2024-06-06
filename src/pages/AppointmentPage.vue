<template>
  <q-page padding>
    <div class="row q-pa-md q-col-gutter-md">
      <div class="col-4 text-center">
        <q-date
          v-model="selectedDate"
          mask="YYYY-MM-DD"
          format="YYYY-MM-DD"
          @update:model-value="fetchTime"
          label="Select Date"
          :options="enableFutureDates"
        />
      </div>
      <div class="col-4" v-if="selectedDate">
        <div class="q-mb-md">Select Time:</div>
        <div class="row q-gutter-sm">
          <q-btn
            :disable="notAvailableTimes.includes(time)"
            v-for="time in generateAvailableTimes"
            :key="time"
            :label="time"
            :text-color="selectedTime === time ? 'white' : 'primary'"
            :color="selectedTime === time ? 'primary' : 'white'"
            @click="selectTime(time)"
            class="q-mb-sm"
          />
        </div>
        <q-btn
          class="q-mt-md"
          color="primary"
          label="Book Appointment"
          icon="pending_actions"
          @click="
            action = 'book';
            dialogLayout = true;
          "
        />
      </div>
    </div>

    <q-table
      wrap-cells
      flat
      bordered
      title="My appointment"
      :rows="reservations"
      :columns="columns"
      row-key="productCode"
    >
      <template v-slot:body="props">
        <q-tr :props="props">
          <q-td key="name" :props="props">
            {{ props.row.user.name }}
          </q-td>
          <q-td key="phoneNumber" :props="props">
            {{ props.row.user.phoneNumber }}
          </q-td>
          <q-td key="email" :props="props">
            {{ props.row.user.email }}
          </q-td>
          <q-td key="address" :props="props">
            {{ props.row.user.address }}
          </q-td>
          <q-td key="petType" :props="props">
            {{ props.row.petType }}
          </q-td>
          <q-td key="breed" :props="props">
            {{ props.row.breed }}
          </q-td>
          <q-td key="petAge" :props="props">
            {{ props.row.petAge }}
          </q-td>
          <q-td key="services" :props="props">
            {{ props.row.services }}
          </q-td>
          <q-td key="date" :props="props">
            {{ props.row.date }}
          </q-td>
          <q-td key="time" :props="props">
            {{ props.row.time }}
          </q-td>
          <q-td key="status" :props="props">
            {{ props.row.status }}
          </q-td>
          <q-td key="_id" :props="props">
            <q-btn
              color="primary"
              @click="
                action = 'view';
                appointmentDetails = props.row;
                dialogLayout = true;
              "
              label="View"
              icon="visibility"
              dense
            />
          </q-td>
        </q-tr>
      </template>
    </q-table>

    <q-dialog v-model="dialogLayout">
      <q-layout view="Lhh lpR fff" container class="bg-white text-dark">
        <q-header class="bg-primary">
          <q-toolbar>
            <q-toolbar-title>{{
              action === "book" ? "Add additional details" : "View details"
            }}</q-toolbar-title>
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
            <q-btn
              @click="submit()"
              color="primary"
              icon="save"
              :label="action === 'book' ? 'Book now' : 'Save'"
            />
          </q-toolbar>
        </q-footer>

        <q-page-container>
          <q-page padding class="q-col-gutter-md">
            <q-input
              outlined
              v-model="appointmentDetails.petType"
              label="Pet Type"
              :readonly="action === 'view'"
            />
            <q-input
              outlined
              v-model="appointmentDetails.breed"
              label="Breed"
              :readonly="action === 'view'"
            />
            <q-input
              outlined
              v-model="appointmentDetails.petAge"
              label="Pet Age"
              :readonly="action === 'view'"
            />
            <q-input
              outlined
              v-model="appointmentDetails.services"
              label="Services"
              :readonly="action === 'view'"
            />
            <q-select
              v-if="action !== 'book'"
              outlined
              :options="['Pending', 'Approved', 'Declined']"
              label="Status"
              v-model="appointmentDetails.status"
            />
          </q-page>
        </q-page-container>
      </q-layout>
    </q-dialog>
  </q-page>
</template>

<script setup>
import { ref, computed, getCurrentInstance, onMounted } from "vue";

import { date, useQuasar } from "quasar";

const $q = useQuasar();
const quasarDate = date;
const selectedDate = ref(quasarDate.formatDate(Date.now(), "YYYY-MM-DD"));
const selectedTime = ref("");
const { $api } = getCurrentInstance().appContext.config.globalProperties;
const reservations = ref([]);
let user = $q.localStorage.getItem("user");

const columns = [
  {
    name: "name",
    required: true,
    label: "Name",
    sortable: true,
    align: "left",
  },
  {
    name: "phoneNumber",
    align: "phoneNumber",
    label: "Phone Number",
    field: "phoneNumber",
    sortable: true,
    align: "left",
  },
  {
    name: "email",
    label: "Email",
    field: "email",
    sortable: true,
    align: "left",
  },
  { name: "address", label: "Address", field: "address", align: "left" },
  { name: "petType", label: "Pet Type", field: "petType", align: "left" },
  { name: "breed", label: "Breed", field: "breed", align: "left" },
  { name: "petAge", label: "Pet Age", field: "petAge", align: "left" },
  { name: "services", label: "Services", field: "services", align: "left" },
  { name: "date", label: "Date", field: "date", align: "left" },
  { name: "time", label: "Time", field: "time", align: "left" },
  { name: "status", label: "Status", field: "status", align: "left" },
  { name: "_id", label: "Action", field: "_id", align: "left" },
];

const dialogLayout = ref(false);
const action = ref("");
const notAvailableTimes = ref([]);

const appointmentDetails = ref({
  petType: "",
  breed: "",
  petAge: "",
  services: "",
  status: "",
});

onMounted(() => {
  let url = "";
  if (user.type === "user") {
    url = `/reservations?&filters[user][id][$eq]=${user.id}&sort=updatedAt:desc&populate=*`;
  } else {
    url = `/reservations?&sort=updatedAt:desc&populate=*`;
  }

  $api
    .get(url)
    .then((response) => {
      console.log("reservations", response.data.data);
      reservations.value = response.data.data;
    })
    .catch((error) => {
      console.log("error", error);
    });

  fetchTime(quasarDate.formatDate(Date.now(), "YYYY-MM-DD"));
});

const enableFutureDates = (date) => {
  const timeStamp = Date.now();
  return date >= quasarDate.formatDate(timeStamp, "YYYY/MM/DD");
};
const generateAvailableTimes = computed(() => {
  const times = [];
  const startHour = 8;
  const endHour = 17;
  for (let hour = startHour; hour <= endHour; hour++) {
    times.push(formatTime(hour, 0));
    // times.push(formatTime(hour, 30));
  }
  return times;
});

const formatTime = (hour, minute) => {
  const period = hour < 12 ? "AM" : "PM";
  const adjustedHour = hour % 12 === 0 ? 12 : hour % 12;
  return `${padZero(adjustedHour)}:${padZero(minute)} ${period}`;
};
const padZero = (number) => {
  return number < 10 ? `0${number}` : number;
};
const selectTime = (time) => {
  selectedTime.value = time;
};

const submit = async () => {
  if (action.value === "book") {
    bookNow();
  } else if (action.value === "view") {
    updateAppointment();
  }
};

const bookNow = async (row) => {
  const payload = {
    data: {
      petType: appointmentDetails.value.petType,
      breed: appointmentDetails.value.breed,
      petAge: appointmentDetails.value.petAge,
      services: appointmentDetails.value.services,
      status: "Pending",
      date: selectedDate.value,
      time: selectedTime.value,
      user: 1,
    },
  };

  const response = await $api.post("/reservations?populate=*", payload);
  console.log(response.data.data);
  reservations.value.unshift(response.data.data);
  dialogLayout.value = false;
};

const updateAppointment = async () => {
  const payload = {
    data: {
      status: appointmentDetails.value.status,
    },
  };

  const response = await $api.put(
    `/reservations/${appointmentDetails.value.id}`,
    payload
  );
  console.log(response);
  dialogLayout.value = false;
};

const fetchTime = (value, reason, details) => {
  console.log("value", value);
  console.log("value", reason);
  console.log("details", details);
  $api
    .get(`/reservations?filters[date][$eq]=${value}`)
    .then((response) => {
      notAvailableTimes.value = response.data.data.map(
        (reservation) => reservation.time
      );
    })
    .catch((error) => {
      console.log("error", error);
    });
};
</script>

<style scoped></style>
