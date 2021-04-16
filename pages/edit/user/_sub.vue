<template>
<v-card class="mx-auto my-12 px-10 py-10" :width="width">
  <validation-observer ref="observer" v-slot="{ invalid, handleSubmit }">
    <form @submit.prevent="handleSubmit(onSubmit)">
      <validation-provider
        v-slot="{ errors }"
        name="Username"
        rules="required"
      >
        <v-text-field
          v-model="formData.username"
          :error-messages="errors"
          label="Username (e.g. John D.)"
          required
        ></v-text-field>
      </validation-provider>

      <validation-provider v-slot="{ errors }" name="Prep" rules="required">
        <v-text-field
          disabled
          v-model="formData.email"
          :error-messages="errors"
          label="email"
          required
        ></v-text-field>
      </validation-provider>

      <v-btn class="mr-4" type="submit" :disabled="invalid"> submit </v-btn>
      <!-- <v-btn @click="clear"> clear </v-btn> -->
    </form>
  </validation-observer>
</v-card>
</template>

<script>
import { required, digits, email, max, regex } from "vee-validate/dist/rules";
import {
  extend,
  ValidationObserver,
  ValidationProvider,
  setInteractionMode,
} from "vee-validate";

setInteractionMode("eager");

extend("digits", {
  ...digits,
  message: "{_field_} needs to be {length} digits. ({_value_})",
});

extend("required", {
  ...required,
  message: "{_field_} can not be empty",
});

extend("max", {
  ...max,
  message: "{_field_} may not be greater than {length} characters",
});

extend("regex", {
  ...regex,
  message: "{_field_} {_value_} does not match {regex}",
});

extend("email", {
  ...email,
  message: "Email must be valid",
});

export default {
  components: {
    ValidationProvider,
    ValidationObserver,
  },
  data() {
    return {
      formData: {
        username: "",
        email: "",
        sub: this.CryptoJS.SHA1(this.$auth.user.sub).toString()
      },
    };
  },
  methods: {
    submit() {
      this.$refs.observer.validate();
    },
    clear() {
      this.name = "";
      this.phoneNumber = "";
      this.email = "";
      this.select = null;
      this.checkbox = null;
      this.$refs.observer.reset();
    },
    onSubmit() {
      this.putUser();
    },
    async putUser() {
      await this.$axios.$put(
        "/edit/user/" + this.formData.sub + "/",
        this.formData
      );
      console.log(this.formData);
      // setTimeout(this.redirect, 1000);
    },
    // async deleteRecipe() {
    //   await this.$axios.$delete(
    //     "/edit/" + this.formData.slug + "/"
    //   );
    //   this.$router.replace({ path: "/" });
    // },
    // redirect() {
    //   this.$router.replace({ path: `/recipe/${this.formData.slug}` });
    // }
  },
  async asyncData({ params, $axios }) {
    const sub= params.sub; // When calling /abc the slug will be "abc"
    let formData = await $axios.$get("/edit/user/" + sub);
    // formData.username = formData.cuisine.id;
    // formData.food_category = formData.food_category.id;
    // formData.course = formData.course.id;
    // console.log(formData.cuisine);
    // // items = items;
    // console.log(formData.course);
    // console.log(items);
    return { sub, formData };
  },
};
</script>