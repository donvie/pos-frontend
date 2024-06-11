<template>
  <q-page padding class="flex flex-center">
    <q-card class="q-pa-md" style="width: 70%; border-radius: 15px">
      <q-form
        @submit="onSubmit"
        @reset="onReset"
        class="text-center q-pa-xl"
      >
        <q-img
          class="q-mb-xl"
          style="height: 144px; width: 144px"
          src="https://pngfre.com/wp-content/uploads/Cat-Paw-Print-10.png"
        />
        <div class="row q-col-gutter-md">
          <!-- <div class="col-3">
            <q-input
              filled
              v-model="username"
              class="q-mb-md"
              label="Username"
              lazy-rules
              :rules="[(val) => (val && val.length > 0) || 'Field cannot be empty']"
            />
          </div> -->
          <div class="col-3">
            <q-input
              filled
              v-model="firstName"
              class="q-mb-md"
              label="First Name"
              lazy-rules
              :rules="[(val) => (val && val.length > 0) || 'Field cannot be empty']"
            />
          </div>
          <div class="col-3">
            <q-input
              filled
              v-model="middleName"
              class="q-mb-md"
              label="Middle Name"
              lazy-rules
              :rules="[(val) => (val && val.length > 0) || 'Field cannot be empty']"
            />
          </div>
          <div class="col-3">
            <q-input
              filled
              v-model="lastName"
              class="q-mb-md"
              label="Last Name"
              lazy-rules
              :rules="[(val) => (val && val.length > 0) || 'Field cannot be empty']"
            />
          </div>
          <div class="col-3">
            <q-input
              filled
              v-model="suffixName"
              class="q-mb-md"
              label="Suffix Name"
              lazy-rules
              :rules="[(val) => (val && val.length > 0) || 'Field cannot be empty']"
            />
          </div>
          <div class="col-3">
            <q-select :rules="[(val) => (val)  || 'Field cannot be empty']" option-label="name" filled v-model="region" :options="phil.regions" label="Region" />
          </div>
          <div class="col-3">
            <q-select :rules="[(val) => (val) || 'Field cannot be empty']" option-label="name" filled v-model="province" :options="phil.getProvincesByRegion(region?.reg_code)" label="Province" />
          </div>
          <div class="col-3">
            <q-select :rules="[(val) => (val) || 'Field cannot be empty']" option-label="name" filled v-model="cityMunicipality" :options="phil.getCityMunByProvince(province?.prov_code)" label="City/Municipality" />
          </div>
          <div class="col-3">
            <q-select :rules="[(val) => (val) || 'Field cannot be empty']" option-label="name" filled v-model="barangay" :options="phil.getBarangayByMun(cityMunicipality?.mun_code)" label="Barangay" />
          </div>
          <div class="col-3">
            <q-select
              filled
              class="q-mb-md"
              v-model="gender"
              :options="['Male', 'Female']"
              label="Gender"
              :rules="[(val) => (val && val.length > 0) || 'Field cannot be empty']"
            />
          </div>
          <div class="col-3">
            <q-input
              filled
              v-model="address"
              class="q-mb-md"
              label="Address"
              lazy-rules
              :rules="[(val) => (val && val.length > 0) || 'Field cannot be empty']"
            />
          </div>
          <div class="col-3">
            <q-input
              filled
              type="email"
              v-model="email"
              label="Email"
              class="q-mb-md"
              lazy-rules
              :rules="[(val) => (val && val.length > 0) || 'Email cannot be empty']"
            />
          </div>
          <div class="col-3">
            <q-input
              filled
              class="q-mb-md"
              mask="(+63)##########"
              fill-mask
              v-model="phoneNumber"
              :rules="[(val) => (val && val.length > 0) || 'Field cannot be empty']"
              label="Phone Number"
            />
          </div>
          <div class="col-3">
            <q-input
              label="Password"
              v-model="password"
              class="q-mb-md"
              filled
              :type="isPwd ? 'password' : 'text'"
              :rules="[
                (val) => (val && val.length > 0) || 'Password cannot be empty',
              ]"
            >
              <template v-slot:append>
                <q-icon
                  :name="isPwd ? 'visibility_off' : 'visibility'"
                  class="cursor-pointer"
                  @click="isPwd = !isPwd"
                />
              </template>
            </q-input>

          </div>
          <div class="col-3">
            <q-input
              class="q-mb-md"
              label="Confirm Password"
              v-model="confirmPassword"
              filled
              :type="isPwd ? 'password' : 'text'"
              :rules="[(val) => (val && val.length > 0) || 'Email cannot be empty']"
            >
              <template v-slot:append>
                <q-icon
                  :name="isPwd ? 'visibility_off' : 'visibility'"
                  class="cursor-pointer"
                  @click="isPwd = !isPwd"
                />
              </template>
            </q-input>
          </div>
        </div>

        <q-card-actions align="center" vertical>
          <q-btn
            style="width: 30%"
            color="primary"
            class="q-mb-md"
            type="submit"
            label="Submit"
          />
          <q-btn
            type="reset"
            to="/login"
            flat
            style="width: 30%"
            color="primary"
            label="Back to login"
          />
        </q-card-actions>
      </q-form>
    </q-card>
  </q-page>
</template>

<script setup>
import { useRouter } from "vue-router";
import { useQuasar } from "quasar";
import { ref, getCurrentInstance } from "vue";
import phil from 'phil-reg-prov-mun-brgy'

import { uid } from "quasar";

defineOptions({
  name: "IndexPage",
});

console.log(phil);

const { $api } = getCurrentInstance().appContext.config.globalProperties;

const $q = useQuasar();
const $router = useRouter();

const firstName = ref(null);
const middleName = ref(null);
const lastName = ref(null);
const suffixName = ref(null);
const region = ref();
const province = ref(null);
const cityMunicipality = ref(null)
const barangay = ref(null);;
const username = ref(null);
const gender = ref(null);
const email = ref(null);
const password = ref(null);
const confirmPassword = ref(null);
const address = ref(null);
const phoneNumber = ref(null);
const isPwd = ref(true);

const signUp = async () => {
  $q.loading.show({
    delay: 400, // ms
  });
  try {
    const response = await $api.post("/auth/local/register", {
      username: email.value,
      email: email.value,
      password: password.value,
      firstName: firstName.value,
      middleName: middleName.value,
      lastName: lastName.value,
      suffixName: suffixName.value,
      region: region.value,
      province: province.value,
      cityMunicipality: cityMunicipality.value,
      barangay: barangay.value,
      gender: gender.value,
      address: address.value,
      phoneNumber: phoneNumber.value,
      type: "user",
    });
    $q.notify({
      type: "positive",
      message: "Success!",
    });

    $router.push("/login");
  } catch (error) {
    // Handle error
    console.error("Registration failed:", error);
    $q.notify({
      color: "negative",
      position: "top",
      message: "Registration failed. Please try again later.",
    });
  } finally {
    $q.loading.hide();
  }
};

const onSubmit = async () => {
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
      signUp();
    })
    .onCancel(() => {
      // console.log('>>>> Cancel')
    })
    .onDismiss(() => {
      // console.log('I am triggered on both OK and Cancel')
    });
};

const onReset = () => {};
</script>
