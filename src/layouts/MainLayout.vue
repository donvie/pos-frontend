<template>
  <q-layout view="lHh Lpr lFf" style="background: #eaebef">
    <q-header outlined>
      <q-toolbar style="height: 60px">
        <q-btn flat @click="drawer = !drawer" round dense icon="menu" />

        <q-toolbar-title class="text-center">
          <q-img
            class="q-mr-md"
            style="height: 54px; width: 54px"
            src="https://pngfre.com/wp-content/uploads/Cat-Paw-Print-10.png"
          />
          PET MATTERS
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="drawer"
      show-if-above
      :mini="miniState"
      @mouseover="miniState = false"
      @mouseout="miniState = true"
      :width="220"
      :breakpoint="500"
      bordered
      class="bg-primary text-white"
    >
      <q-scroll-area class="fit" :horizontal-thumb-style="{ opacity: 0 }">
        <q-list padding>
          <q-item
            v-if="user.type === 'admin'"
            exact-active-class="bg-blue-10 text-white"
            exact
            to="/"
            clickable
            v-ripple
          >
            <q-item-section avatar>
              <q-icon name="dashboard" />
            </q-item-section>

            <q-item-section>Dashboard</q-item-section>
          </q-item>

          <q-item
            exact-active-class="bg-blue-10 text-white"
            exact
            to="/pos"
            clickable
            v-if="user.type === 'cashier'"
            v-ripple
          >
            <q-item-section avatar>
              <q-icon name="list_alt" />
            </q-item-section>

            <q-item-section>POS </q-item-section>
          </q-item>

          <q-item
            exact-active-class="bg-blue-10 text-white"
            exact
            v-if="user.type === 'admin'"
            to="/product"
            clickable
            v-ripple
          >
            <q-item-section avatar>
              <q-icon name="inventory_2" />
            </q-item-section>

            <q-item-section>Products</q-item-section>
          </q-item>

          <q-item
            exact-active-class="bg-blue-10 text-white"
            exact
            to="/sale"
            clickable
            v-ripple
            v-if="user.type === 'admin'"
          >
            <q-item-section avatar>
              <q-icon name="payments" />
            </q-item-section>

            <q-item-section>Sales</q-item-section>
          </q-item>

          <q-item
            exact-active-class="bg-blue-10 text-white"
            exact
            to="/sales-report"
            clickable
            v-ripple
            v-if="user.type === 'admin'"
          >
            <q-item-section avatar>
              <q-icon name="grid_view" />
            </q-item-section>

            <q-item-section>Sales Report</q-item-section>
          </q-item>

          <q-item
            exact-active-class="bg-blue-10 text-white"
            exact
            to="/my-appointment"
            clickable
            v-if="user.type !== 'cashier'"
            v-ripple
          >
            <q-item-section avatar>
              <q-icon v-if="!miniState" name="event_available" />
              <q-btn v-if="miniState" flat dense icon="event_available">
                <q-badge color="red" floating>{{reservationNewCount}}</q-badge>
              </q-btn>
            </q-item-section>

            <q-item-section><div>My Appointment <span class="text-weight-bold text-blue">({{reservationNewCount}})</span></div></q-item-section>
          </q-item>

          <q-item
            clickable
            v-ripple
            @click="logOut()"
            class="fixed-bottom q-mb-md"
          >
            <q-item-section avatar>
              <q-icon name="logout" />
            </q-item-section>

            <q-item-section>Logout</q-item-section>
          </q-item>
        </q-list>
      </q-scroll-area>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup>
import { useRouter } from "vue-router";
import { useQuasar } from "quasar";
import { ref, computed, getCurrentInstance, onMounted } from "vue";

defineOptions({
  name: "MainLayout",
});

const { $api } = getCurrentInstance().appContext.config.globalProperties;
const $q = useQuasar();
const $router = useRouter();

const drawer = ref(false);
const miniState = ref(true);
const reservationNewCount = ref(0);

let user = $q.localStorage.getItem("user");

const leftDrawerOpen = ref(false);

onMounted(() => {
  fethcReservation()

  if (user.type === "user") {
    $router.push("/my-appointment");
  }
  if (user.type === "admin") {
    $router.push("/");
  }
  if (user.type === "cashier") {
    $router.push("/pos");
  }
});

const fethcReservation = () => {
  let url = `/reservations?&filters[isViewed][$eq]=false&pagination[limit]=5000&sort=updatedAt:desc&populate=*`;

  $api
    .get(url)
    .then((response) => {
      reservationNewCount.value = response.data.meta.pagination.total
      console.log('dads', response)
    })
    .catch((error) => {
      console.log("error", error);
    });
}


const logOut = () => {
  $q.dialog({
    title: "Confirm",
    message: "Are you sure you want to logout?",
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
      $q.localStorage.removeItem("user");
      $router.push("/login");
    })
    .onCancel(() => {
      // console.log('>>>> Cancel')
    })
    .onDismiss(() => {
      // console.log('I am triggered on both OK and Cancel')
    });
};
</script>
