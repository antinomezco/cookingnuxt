<template>
  <!-- <div v-if="this.CryptoJS.SHA1(this.$auth.user.sub).toString() != correctUser">
    Cannot edit this recipe, redirecting
  </div> -->
  <div>
  <validation-observer
    ref="observer"
    v-slot="{ invalid, handleSubmit }"
  >
    <form @submit.prevent="handleSubmit(onSubmit)">
      <validation-provider
        v-slot="{ errors }"
        name="Recipe name"
        rules="required"
      >
        <v-text-field
          v-model="formData.recipe_name"
          :error-messages="errors"
          label="Name"
          required
        ></v-text-field>
      </validation-provider>

      <input type="file" @change="previewImage" accept="image/*" />

      <validation-provider v-slot="{ errors }" name="Prep" rules="required">
        <v-text-field
          v-model="formData.prep_time"
          :error-messages="errors"
          label="Prep time (e.g. 15 mins or 1:30 hours)"
          required
        ></v-text-field>
      </validation-provider>

      <validation-provider v-slot="{ errors }" name="Cook" rules="required">
        <v-text-field
          v-model="formData.cook_time"
          :error-messages="errors"
          label="Cook time (e.g. 15 mins or 1:30 hours)"
          required
        ></v-text-field>
      </validation-provider>

      <validation-provider v-slot="{ errors }" name="Servings" rules="required">
        <v-text-field
          v-model="formData.servings"
          :error-messages="errors"
          label="How many servings (e.g. 4)"
          required
        ></v-text-field>
      </validation-provider>

      <validation-provider
        v-slot="{ errors }"
        name="Recipe description"
        rules="required"
      >
        <v-textarea
          v-model="formData.recipe_description"
          :error-messages="errors"
          auto-grow
          label="Short recipe description"
          required
        ></v-textarea>
      </validation-provider>

      <validation-provider
        v-slot="{ errors }"
        name="Ingredients"
        rules="required"
      >
        <v-textarea
          v-model="formData.ingredients_text"
          :error-messages="errors"
          auto-grow
          label="Ingredients (one ingredient per line)"
          required
        ></v-textarea>
      </validation-provider>

      <validation-provider
        v-slot="{ errors }"
        name="Recipe directions"
        rules="required"
      >
        <v-textarea
          v-model="formData.recipe_steps"
          :error-messages="errors"
          auto-grow
          label="Directions (one step per line)"
          required
        ></v-textarea>
      </validation-provider>

        <v-textarea
          v-model="formData.recipe_notes"
          auto-grow
          label="Recipe notes (if you feel they're needed)"
        ></v-textarea>

      <validation-provider v-slot="{ errors }" name="Course" rules="required">
        <v-select
          item-text="course_name"
          item-value="id"
          v-model="formData.course"
          :items="courseList"
          :error-messages="errors"
          label="Course"
          data-vv-name="courseList"
          required
        ></v-select>
      </validation-provider>

      <validation-provider v-slot="{ errors }" name="Cuisine" rules="required">
        <v-select
          item-text="cuisine_name"
          item-value="id"
          v-model="formData.cuisine"
          :items="cuisineList"
          :error-messages="errors"
          label="Cuisine"
          data-vv-name="selectdatavvcuisine"
          required
        ></v-select>
      </validation-provider>
      <validation-provider
        v-slot="{ errors }"
        name="Faood category"
        rules="required"
      >
        <v-select
          item-text="food_category_name"
          item-value="id"
          v-model="formData.food_category"
          :items="food_categoryList"
          :error-messages="errors"
          label="Food Category"
          data-vv-name="selectdatavvfood"
          required
        ></v-select>
      </validation-provider>
      <!-- <validation-provider v-slot="{ errors }" rules="required" name="checkbox">
        <v-checkbox
          v-model="checkbox"
          :error-messages="errors"
          value="1"
          label="Option"
          type="checkbox"
          required
        ></v-checkbox>
      </validation-provider> -->

      <v-btn class="mr-4" type="submit" :disabled="invalid"> submit </v-btn>
      <v-btn @click="clear"> clear </v-btn>
      <v-btn @click="deleteRecipe"> delete </v-btn>
    </form>
  </validation-observer>
  <v-snackbar
      v-model="snackbar"
      :timeout="timeout"
    >
      {{snackbarMessage}}
    </v-snackbar>
  </div>
</template>

<script>
import { required, digits, email, max, regex } from "vee-validate/dist/rules";
import firebase from "firebase";
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
  middleware: "auth",
  components: {
    ValidationProvider,
    ValidationObserver,
  },
  data() {
    return {
      formData: {
        recipe_name: "",
        prep_time: "",
        cook_time: "",
        servings: "",
        image: "",
        recipe_description: "",
        ingredients_text: "",
        recipe_steps: "",
        recipe_notes: "",
        course: "",
        food_category: "",
        cuisine: "",
        // slug: "",
      },
      courseList: [],
      food_categoryList: [],
      cuisineList: [],
      correctUser: "",
      imageAdded: false,
      imageTemp: "",
      //
      snackbar: false,
      timeout: 2000,
      snackbarMessage: ""
    };
  },
  computed:{

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
      if(this.CryptoJS.SHA1(this.$auth.user.sub).toString() != this.correctUser){
      return alert("Cannot edit other user's recipes")
      }
      // this.slugify(this.formData.recipe_name);
      // this.putRecipe();
      this.onUpload();
    },
    slugify(str) {
      str = str.replace(/^\s+|\s+$/g, "");

      // Make the string lowercase
      str = str.toLowerCase();

      // Remove accents, swap ñ for n, etc
      var from =
        "ÁÄÂÀÃÅČÇĆĎÉĚËÈÊẼĔȆÍÌÎÏŇÑÓÖÒÔÕØŘŔŠŤÚŮÜÙÛÝŸŽáäâàãåčçćďéěëèêẽĕȇíìîïňñóöòôõøðřŕšťúůüùûýÿžþÞĐđßÆa·/_,:;";
      var to =
        "AAAAAACCCDEEEEEEEEIIIINNOOOOOORRSTUUUUUYYZaaaaaacccdeeeeeeeeiiiinnooooooorrstuuuuuyyzbBDdBAa------";
      for (var i = 0, l = from.length; i < l; i++) {
        str = str.replace(new RegExp(from.charAt(i), "g"), to.charAt(i));
      }

      // Remove invalid chars
      str = str
        .replace(/[^a-z0-9 -]/g, "")
        // Collapse whitespace and replace by -
        .replace(/\s+/g, "-")
        // Collapse dashes
        .replace(/-+/g, "-");

      this.formData.slug = str;
    },
    previewImage(event) {
      // this.uploadValue = 0;
      // this.picture = null;
      this.imageAdded = true
      // alert('this.imageAdded: ' + this.imageAdded)
      // alert('this.imageTemp: ' + this.imageTemp)
      this.imageData = event.target.files[0];
    },
    onUpload() {
      // this.image = null;

      const storageRef = firebase
        .storage()
        .ref(
          `${this.CryptoJS.SHA1(
            this.$auth.user.sub
          ).toString()}/${this.CryptoJS.SHA1(this.formData.slug).toString()}`
        )
        .put(this.imageData);
      storageRef.on(
        `state_changed`,
        (snapshot) => {
          // this.uploadValue = (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
        },
        (error) => {
          console.log(error.message);
        },
        () => {
          // this.uploadValue = 100;
          storageRef.snapshot.ref.getDownloadURL().then((url) => {
            this.formData.image = url;
            console.log(url);
            console.log(this.formData.image);
            this.putRecipe();
          });
        }
      );
    },
    async putRecipe() {
      if(!this.imageAdded){
        this.formData.image = this.imageTemp
      }
      await this.$axios.$put(
        "/edit/recipe/" + this.formData.slug + "/",
        this.formData
      );
      console.log(this.formData);
      this.snackbarMessage = "Recipe has been edited successfully"
      this.snackbar = true
      setTimeout(this.redirect, 2000);
    },
    async deleteRecipe() {
      await this.$axios.$delete("/edit/recipe/" + this.formData.slug + "/");
      this.snackbarMessage = "Recipe has been edited successfully"
      setTimeout(this.$router.replace({ path: "/"}), 2000);
    },
    redirect() {
      this.$router.replace({ path: `/recipe/${this.formData.slug}` });
    },
  },
  async asyncData({ params, $axios }) {
    const slug = params.slug; // When calling /abc the slug will be "abc"
    let formData = await $axios.$get("/edit/recipe/" + slug);
    console.log(formData);
    formData.cuisine = formData.cuisine.id;
    formData.food_category = formData.food_category.id;
    const imageTemp = formData.image;
    formData.course = formData.course.id;
    const correctUser = formData.user.sub;
    formData.user = formData.user.id;
    // console.log(formData.cuisine)
    // items = items;
    // console.log(formData.course);
    // console.log(items);
    return { slug, formData, correctUser, imageTemp };
  },
  async mounted() {
    // if (this.CryptoJS.SHA1(this.$auth.user.sub).toString() === correctUser) {
    //   console.log(correctUser);
    // }
    let preCuisine = await this.$axios.$get("/allcuisines");
    console.log("preCuisine");
    console.log(preCuisine);
    this.cuisineList = preCuisine;

    let preFoodCategory = await this.$axios.$get("/allfoodcategories");
    console.log("preFoodCategory");
    console.log(preFoodCategory);
    this.food_categoryList = preFoodCategory;

    // this.ingredientList = ingredients

    let preCourses = await this.$axios.$get("/allcourses");
    console.log("preCourses ");
    console.log(preCourses);
    this.courseList = preCourses;

    console.log(this.formData);
  },
};
</script>