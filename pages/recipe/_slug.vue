<template>
  <div class="home">
    <v-card class="mx-auto my-12" :width="width">
      <div>
        <v-img
          class="white--text justify-start rounded-t-lg"
          max-height="400"
          width="100%"
          :src="items.image"
          :lazy-src="require(`@/assets/placeholder.jpg`)"
        >
          <template v-slot:placeholder>
            <v-row class="fill-height ma-0" align="center" justify="center">
            </v-row>
          </template>
          <v-toolbar
            color="white"
            min-width="50"
            flat
            height="35"
            tile
            floating
            class="align-start rounded-r black--text"
            style="margin-top: 8px"
          >
            {{ items.course.course_name }}
          </v-toolbar>
        </v-img>
      </div>
      <hr />
      <v-card-text>
        <div
          class="d-flex justify-center text-h3 my-8"
          style="overflow-wrap: normal"
          ><div>{{ items.recipe_name }}</div>
        </div>
        <hr />
        <!-- <v-row align="center" class="mx-0">
          <v-rating
            :value="4.5"
            color="amber"
            dense
            half-increments
            readonly
            size="14"
          ></v-rating>

          <div class="grey--text ml-4">4.5 (413)</div>
        </v-row> --><v-btn
          v-if="
            this.$auth.user &&
            this.CryptoJS.SHA1(this.$auth.user.sub).toString() ===
              items.user.sub
          "
          color="white"
          min-width="50"
          height="35"
          :to="'/edit/recipe/' + items.slug"
          floating
          class="black--text"
        >
          Edit Recipe
        </v-btn>

        <div
          class="d-flex justify-space-between flex-wrap my-md-3 my-lg-3 my-xl-3"
        >
          <div class="my-2"></div>
          <div>
            <div class="subtitle-1 font-weight-black">Prep time:</div>
            <div class="subtitle-1">{{ items.prep_time }}</div>
          </div>
          <div class="my-2"></div>
          <div class="my-2"></div>
          <div class="my-2"></div>
          <div>
            <div class="subtitle-1 font-weight-black">Cook time:</div>
            <div class="subtitle-1">{{ items.cook_time }}</div>
          </div>
          <!-- <div class="my-2"></div> -->
          <div class="my-2"></div>
          <div class="my-2"></div>
          <div class="my-2"></div>
          <div>
            <div class="subtitle-1 font-weight-black">Yields:</div>
            <div class="subtitle-1">{{ items.servings }} servings</div>
          </div>
          <div class="my-2"></div>
        </div>

        <div class="my-2"></div>

        <v-divider class="mx-4" v-show="items.recipe_description"></v-divider>

        <v-card-title v-show="items.recipe_description"
          >Recipe description:
        </v-card-title>
        <p class="subtitle-1 px-7" v-show="items.recipe_description">
          {{ items.recipe_description }}
        </p>

        <v-divider class="mx-4"></v-divider>

        <v-card-title>Ingredients: </v-card-title>

        <p
          v-for="ing in items.ingredients_text.split('\r\n')"
          :key="ing"
          class="px-7 font-weight-medium"
        >
          {{ ing }}
        </p>

        <v-divider class="mx-4"></v-divider>

        <v-card-title>Directions: </v-card-title>
        <p
          v-for="dir in items.recipe_steps.split('\r\n')"
          :key="dir"
          class="pl-7 pr-9 font-weight-medium"
        >
          {{ dir }}
        </p>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
// import Loading from "../components/static/Loading.vue";
export default {
  // components: { Loading },
  // head() {
  //   return {
  //     title: this.text,
  //     meta: [
  //       {
  //         hid: "description",
  //         name: "description",
  //         content: "Home page description",
  //       },
  //     ],
  //   };
  // },
  data() {
    return {
      // text: this.oneBusiness.business_name,
      currentPage: "",
      currentSlug: "",
      oneBusiness: {},
      loading: true,
      items: "",
    };
  },
  // created() {
  //   this.readDataFromAPI();
  // },
  computed: {
    width() {
      switch (this.$vuetify.breakpoint.name) {
        case "xs":
          return "85%";
        case "sm":
          return "85%";
        case "md":
          return "85%";
        case "lg":
          return "50%";
        case "xl":
          return "50%";
      }
    },
  },
  // watch: {
  //   options: {
  //     handler() {
  //       this.readDataFromAPI();
  //     },
  //   },
  //   deep: true,
  // },
  async asyncData({ params, $axios }) {
    const slug = params.slug; // When calling /abc the slug will be "abc"
    let items = await $axios.$get("/recipe/" + slug);
    console.log(items);
    items = items;
    console.log("items");
    console.log(items);
    return { slug, items };
  },
  // methods: {
  //   onImgLoad() {
  //     // this.Loading = false
  //   },
  //   readDataFromAPI() {
  //     this.loading = true;
  //   },
  // },
};
</script>

<style scoped>
#pcs {
  display: flex;
  justify-content: space-between;
}
</style>