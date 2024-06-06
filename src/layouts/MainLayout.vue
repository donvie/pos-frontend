<template>
  <q-layout view="lHh Lpr lFf" style="background: #eaebef">
    <q-header outlined>
      <q-toolbar style="height: 60px">
        <q-btn flat @click="drawer = !drawer" round dense icon="menu" />

        <q-toolbar-title class="text-center"> PET MATTERS </q-toolbar-title>
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
          >
            <q-item-section avatar>
              <q-icon name="payments" />
            </q-item-section>

            <q-item-section>Sales</q-item-section>
          </q-item>

          <q-item
            exact-active-class="bg-blue-10 text-white"
            exact
            to="/my-appointment"
            clickable
            v-ripple
          >
            <q-item-section avatar>
              <q-icon name="event_available" />
            </q-item-section>

            <q-item-section>My Appointment</q-item-section>
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
import { ref } from "vue";

defineOptions({
  name: "MainLayout",
});

const $q = useQuasar();
const $router = useRouter();

const drawer = ref(false);
const miniState = ref(false);

const leftDrawerOpen = ref(false);

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
    .onOk(() => {
      // console.log('>>>> secondn OK catcher')
    })
    .onCancel(() => {
      // console.log('>>>> Cancel')
    })
    .onDismiss(() => {
      // console.log('I am triggered on both OK and Cancel')
    });
};
</script>
