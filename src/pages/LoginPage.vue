<template>
  <q-page class="flex flex-center">
    <q-card class="q-pa-md" style="width: 400px; border-radius: 15px">
      <q-form
        @submit="onSubmit"
        @reset="onReset"
        class="q-gutter-md text-center"
      >
        <q-img
          style="height: 60px; width: 300px"
          src="https://dev-118-motor-shop.pantheonsite.io/wp-content/uploads/2022/07/TIREEEE-2.png"
        />
        <q-input
          filled
          type="email"
          v-model="email"
          label="Email"
          lazy-rules
          :rules="[(val) => (val && val.length > 0) || 'Email cannot be empty']"
          rounded
        />

        <q-input
          label="Password"
          v-model="password"
          filled
          :type="isPwd ? 'password' : 'text'"
          :rules="[
            (val) => (val && val.length > 0) || 'Password cannot be empty',
          ]"
          rounded
        >
          <template v-slot:append>
            <q-icon
              :name="isPwd ? 'visibility_off' : 'visibility'"
              class="cursor-pointer"
              @click="isPwd = !isPwd"
            />
          </template>
        </q-input>
        <q-card-actions>
          <q-btn
            color="primary"
            class="full-width q-mb-md"
            type="submit"
            label="Login"
            rounded
          />
          <q-btn
            class="full-width"
            type="reset"
            to="signup"
            flat
            color="primary"
            label="Create an account"
            rounded
          />
        </q-card-actions>
      </q-form>
    </q-card>
  </q-page>
</template>

<script setup>
import { useRouter } from "vue-router";
import { useQuasar } from "quasar";
import { ref, onMounted, getCurrentInstance } from "vue";

const { $api } = getCurrentInstance().appContext.config.globalProperties;

defineOptions({
  name: "IndexPage",
});

const $q = useQuasar();
const $router = useRouter();

const email = ref("donvie@gmail.com");
const password = ref("Pass123$");
const isPwd = ref(true);

const onSubmit = () => {
  $q.loading.show({
    delay: 400,
  });

  $api
    .post("/auth/local", {
      identifier: email.value,
      password: password.value,
    })
    .then((response) => {
      let user = { ...{ jwt: response.data.jwt }, ...response.data.user };
      $q.localStorage.set("user", user);

      $q.loading.hide();
      $q.notify({
        type: "positive",
        message: "Success!",
      });

      if (user.type === "admin") {
        $router.push("/");
      } else if (user.type === "user") {
        $router.push("/my-appointment");
      } else if (user.type === "cashier") {
        $router.push("/pos");
      }
    })
    .catch((error) => {
      $q.notify({
        type: "negative",
        message: "Failed",
      });
      $q.loading.hide();
      console.log("An error occurred:", error.response);
    });
};

const onReset = () => {};
</script>
