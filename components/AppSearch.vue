<template>
  <div style="margin: 1%">
    
    <v-container fluid>
      <v-data-iterator
        :items="items"
        :options.sync="options"
        :items-per-page.sync="itemsPerPage"
        :page="page"
        :search="search"
        :sort-by="sortBy.toLowerCase()"
        :sort-desc="sortDesc"
        hide-default-footer
        loading-text="Loading results. If it takes too long, try refreshing the page"
        loading="Loading results"
      >
        <template v-slot:header>
          <v-toolbar dark rounded :height="height" color="grey darken-3 my-3">
            <v-container
              class="justify-center d-flex flex-column flex-lg-row autoheight"
              style="margin-bottom: -30px; min-width: 100%"
            >
              <!-- Filter for business name-->
              <v-text-field
                v-model="tempRecipeFilterValue"
                type="text"
                dark
                label="Recipe (click on icon to search)"
                :append-icon="
                  searchButtonIcon ? 'mdi-magnify' : 'mdi-close-circle'
                "
                @click:append="toggleSeachButtonIcon"
                @keydown.enter="toggleSeachButtonIcon"
              ></v-text-field>
              <!-- change v-model and :items -->
              <v-spacer></v-spacer>
              <v-autocomplete
                item-text="cuisine_name"
                clearable
                dark
                @change="readDataFromAPI()"
                :items="cuisineList"
                v-model="tempCuisineFilterValue"
                cache-items
                label="Cuisine"
              ></v-autocomplete>

              <v-spacer></v-spacer>
              <!-- Filter for calories -->
              <v-autocomplete
                item-text="course_name"
                clearable
                @change="readDataFromAPI()"
                :items="courseList"
                v-model="tempCourseFilterValue"
                label="Course"
              ></v-autocomplete>
              <v-spacer></v-spacer>
              <!-- Filter for calories -->
              <v-autocomplete
                item-text="username"
                item-value="sub"
                clearable
                @change="readDataFromAPI()"
                :items="userList"
                v-model="tempUserFilterValue"
                label="User"
              ></v-autocomplete>
              <v-spacer></v-spacer>
              <!-- </v-row> -->
              <v-btn-toggle
                v-model="sortDesc"
                mandatory
                class="d-flex justify-end"
                background-color="#424242"
                style="margin-bottom: 25px"
              >
                <v-btn
                  large
                  depressed
                  color="grey"
                  @click="preventRecipeSearch = 'true'"
                  :value="false"
                >
                  <v-icon>mdi-arrow-up</v-icon>
                </v-btn>
                <v-btn
                  large
                  depressed
                  color="grey"
                  @click="preventRecipeSearch = 'true'"
                  :value="true"
                >
                  <v-icon>mdi-arrow-down</v-icon>
                </v-btn>
              </v-btn-toggle>
            </v-container>
          </v-toolbar>
        </template>

        <template v-slot:default="props">
          <!-- <div
            v-if="loading"
            class="d-flex justify-center"
            style="margin-top: 5%; margin-bottom: 5%"
          >
            <v-progress-circular
              indeterminate
              size="150"
              width="10"
              color="purple"
            ></v-progress-circular>
          </div>
          <v-row v-else> -->
          <v-row>
            <v-col
              v-for="recipe in props.items"
              :key="recipe.recipe_name"
              cols="12"
              sm="6"
              md="4"
              lg="3"
            >
              <v-card
                class="mx-auto card-outter"
                width="350"
                height="100%"
                outlined
                color="#1d1d1d"
                nuxt          
                :to="{
                  name: 'recipe-slug',
                  params: {
                    slug: recipe.slug,
                  },
                }"
                target="_blank"
              >
                <v-img
                  class="white--text justify-start"
                  height="150"
                  width="350"
                  :src="recipe.image"
                  :lazy-src="require(`@/assets/placeholder.jpg`)"
                  
                >
                  <v-toolbar
                    color="yellow"
                    min-width="40"
                    flat
                    height="20"
                    tile
                    floating
                    class="align-start rounded-r black--text"
                    style="margin-top: 8px; font-size: small"
                  >
                    <span>{{ recipe.course.course_name }}</span>
                  </v-toolbar>
                  <template v-slot:placeholder>
                    <v-row
                      class="fill-height ma-0"
                      align="center"
                      justify="center"
                    >
                      <!-- <v-progress-circular
                        indeterminate
                        color="grey lighten-5"
                      ></v-progress-circular> -->
                    </v-row>
                  </template>
                  <!-- fix src -->
                  <v-card-title
                  class="align-end"
                  style="word-break: break-word"
                  >{{ recipe.recipe_name }}</v-card-title
                >
                </v-img>
                <!-- <hr /> -->
                
                <!-- <v-card-subtitle class="pb-0">
                  <div
                    class="white--text"
                    v-show="recipe.course_id.course_name"
                  >
                    Course: {{ recipe.course_id.course_name }}
                  </div>
                  <div class="white--text" v-show="recipe.phone_number">
                    {{ recipe.phone_number }}
                  </div>
                </v-card-subtitle>

                <v-card-text class="text--primary">
                  <div class="white--text" v-show="recipe.category">
                    {{ recipe.category }}
                  </div>

                  <hr />
                  <div class="white--text">{{ recipe.description }}</div>
                </v-card-text> -->
                <!-- <div class="d-flex align-end">
                  <v-card-actions class="card-actions">
                    <v-btn
                      icon
                      v-show="recipe.facebook"
                      :href="recipe.facebook"
                      target="_blank"
                    >
                      <v-icon color="white" text> mdi-facebook </v-icon>
                    </v-btn>
                    <v-btn
                      icon
                      v-show="recipe.twitter"
                      :href="recipe.twitter"
                      target="_blank"
                    >
                      <v-icon color="white" text> mdi-twitter </v-icon>
                    </v-btn>
                    <v-btn
                      icon
                      v-show="recipe.instagram"
                      :href="recipe.instagram"
                      target="_blank"
                    >
                      <v-icon color="white" text> mdi-instagram </v-icon>
                    </v-btn>
                    <v-btn
                      icon
                      v-show="recipe.youtube"
                      :href="recipe.youtube"
                      target="_blank"
                    >
                      <v-icon color="white" text> mdi-youtube </v-icon>
                    </v-btn>
                    <v-btn
                      icon
                      v-show="recipe.website"
                      :href="recipe.website"
                      target="_blank"
                    >
                      <v-icon color="white" text> mdi-web </v-icon>
                    </v-btn>
                    <v-btn
                      icon
                      v-show="recipe.address"
                      :href="
                        'https://www.google.com/maps/place/' +
                        recipe.address +
                        ', ' +
                        recipe.city +
                        ', ' +
                        recipe.province
                      "
                      target="_blank"
                    >
                      <v-icon color="white" text> mdi-google-maps </v-icon>
                    </v-btn>
                    <v-btn
                      icon
                      v-show="recipe.phone_number"
                      :href="'tel://' + recipe.phone_number"
                      target="_blank"
                    >
                      <v-icon color="white" text> mdi-phone </v-icon>
                    </v-btn>
                  </v-card-actions>
                </div> -->
              </v-card>
            </v-col>
          </v-row>
        </template>

        <template v-slot:footer>
          <v-row
            class="mt-2"
            align="center"
            justify="center"
            v-show="numberOfPages"
          >
            <span class="grey--text">Items per page</span>
            <v-menu offset-y>
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  dark
                  text
                  color="white"
                  class="ml-2"
                  v-bind="attrs"
                  v-on="on"
                >
                  {{ itemsPerPage }}
                  <v-icon>mdi-chevron-down</v-icon>
                </v-btn>
              </template>
              <v-list>
                <v-list-item
                  v-for="(number, index) in itemsPerPageArray"
                  :key="index"
                  @click="updateItemsPerPage(number)"
                >
                  <v-list-item-title>{{ number }}</v-list-item-title>
                </v-list-item>
              </v-list>
            </v-menu>

            <v-spacer></v-spacer>

            <span class="mr-4 grey--text">
              Page {{ page }} of {{ numberOfPages }}
            </span>
            <div>
              <v-btn
                fab
                dark
                bottom
                color="grey darken-3"
                class="mr-1"
                @click="firstPage"
                v-if="page > 1"
              >
                <v-icon>mdi-chevron-double-left</v-icon>
              </v-btn>
              <v-btn
                fab
                dark
                large
                color="grey darken-3"
                class="mr-1"
                @click="formerPage"
                v-if="page > 1"
              >
                <v-icon>mdi-chevron-left</v-icon>
              </v-btn>
              <v-btn
                fab
                dark
                large
                color="grey darken-3"
                class="ml-1"
                @click="nextPage"
                v-if="page < numberOfPages"
              >
                <v-icon>mdi-chevron-right</v-icon>
              </v-btn>
              <v-btn
                fab
                dark
                color="grey darken-3"
                class="ml-1"
                @click="lastPage"
                v-if="page < numberOfPages"
              >
                <v-icon>mdi-chevron-double-right</v-icon>
              </v-btn>
            </div>
          </v-row>
        </template>
      </v-data-iterator>
    </v-container>
  </div>
</template>

<script>
export default {
  head: {
    title: "Constantino's - Recipe search",
    meta: [
      {
        hid: "description",
        name: "description",
        content: "content",
      },
    ],
  },
  data() {
    return {
      currentPage: "",
      expanded: [],
      singleExpand: true,
      // We need some values for our select.
      ingredientList: [],
      cuisineList: [],
      courseList: [],
      userList: [],
      // Filter models.
      recipeFilterValue: "",
      cuisineFilterValue: "",
      ingredientFilterValue: "",
      courseFilterValue: "",
      userFilterValue: "",
      tempRecipeFilterValue: "",
      tempUserFilterValue: "",
      tempCuisineFilterValue: "",
      tempIngredientFilterValue: "",
      tempCourseFilterValue: "",
      // Table data.
      items: [],
      totalRecipes: 0,
      loading: false,
      options: {},
      page: 1,
      searchButtonIcon: true,
      pagination: {},
      sortBy: "recipe_name",
      itemsPerPage: 8,
      itemsPerPageArray: [8, 12],
      search: "",
      filter: {},
      sortDesc: false,
      cityOrRegion: true,
      preventRecipeSearch: false,
    };
  },
  watch: {
    options: {
      handler() {
        this.readDataFromAPI();
      },
    },
    deep: true,
  },
  created() {},
  mounted() {
    this.readDataFromAPI();
  },
  computed: {
    numberOfPages: {
      get: function () {
        return Math.ceil(this.totalRecipes / this.itemsPerPage);
      },
      set: function () {
        //if I don't put a setter, vuejs complains
        //return Math.ceil(this.totalRecipes / this.itemsPerPage);
      },
    },
    height() {
      switch (this.$vuetify.breakpoint.name) {
        case "xs":
          return 305;
        case "sm":
          return 305;
        case "md":
          return 305;
        case "lg":
          return 100;
        case "xl":
          return 100;
      }
    },
    margin() {
      switch (this.$vuetify.breakpoint.name) {
      }
    },
  },
  methods: {
    nextPage() {
      // console.log(this.page + " " + this.numberOfPages)
      if (this.page + 1 <= this.numberOfPages) this.page += 1;
      this.preventRecipeSearch = true;
      this.readDataFromAPI();
      // this.checkForMarkers()
    },
    formerPage() {
      if (this.page - 1 >= 1) this.page -= 1;
      this.preventRecipeSearch = true;
      this.readDataFromAPI();
      // this.checkForMarkers()
    },
    lastPage() {
      // console.log(this.page + " " + this.numberOfPages)
      if (this.page + 1 <= this.numberOfPages) this.page = this.numberOfPages;
      this.preventRecipeSearch = true;
      this.readDataFromAPI();
    },
    firstPage() {
      if (this.page - 1 >= 1) this.page = 1;
      this.preventRecipeSearch = true;
      this.readDataFromAPI();
    },
    updateItemsPerPage(number) {
      this.itemsPerPage = number;
    },
    toggleSeachButtonIcon() {
      this.searchButtonIcon = !this.searchButtonIcon;
      if (!this.searchButtonIcon) {
        this.readDataFromAPI();
      } else {
        this.recipeFilterValue = "";
        this.tempRecipeFilterValue = "";
        this.readDataFromAPI();
      }
    },
    nullUndefinedToBlank(value) {
      if (value === null || value === undefined) {
        return "";
      } else {
        return value;
      }
    },
    async readDataFromAPI() {
      // prevents buttons from performing a search when it's unwanted (sorting or changeing page)
      this.loading = true;
      if (!this.preventRecipeSearch) {
        this.recipeFilterValue = this.nullUndefinedToBlank(
          this.tempRecipeFilterValue
        );
      }
      this.preventRecipeSearch = false;
      this.courseFilterValue = this.nullUndefinedToBlank(
        this.tempCourseFilterValue
      );
      this.ingredientFilterValue = this.nullUndefinedToBlank(
        this.tempIngredientFilterValue
      );
      this.cuisineFilterValue = this.nullUndefinedToBlank(
        this.tempCuisineFilterValue
      );
      this.userFilterValue = this.nullUndefinedToBlank(
        this.tempUserFilterValue
      );
      let { sortBy, sortDesc, page, itemsPerPage } = this.options;
      // console.log("sort by: " + sortBy + " sort desc: " + sortDesc);
      if (sortDesc[0] == true) {
        sortDesc = "";
      } else {
        sortDesc = "-";
      }
      //   this.currentPage = this.$route.path.match(/[a-zA-Z]+$/m).join();
      // console.log(this.currentPage);
      page = this.page;
      let items = await this.$axios.$get(
        "/filterrecipes?" + 
        "recipe_name=" + this.recipeFilterValue +
        "&cuisine_name=" + this.cuisineFilterValue +
        "&course_name=" + this.courseFilterValue +
        "&sub="+ this.userFilterValue +
        "&approved=True" + 
        // "&region=" +
        // this.CourseFilterValue +
        // "&category=" +
        // this.IngredientFilterValue +
        "&size=" +
        itemsPerPage +
        "&page=" +
        page +
        "&sortBy=" +
        sortDesc +
        sortBy
      );
      console.log(items);
      this.totalRecipes = items.count;
      // this.numberOfPages = response.data.totalPages;
      this.items = items.results;
    },
  },
  async created() {
    //this.getPosition() //map stuff
    
    this.tempRecipeFilterValue = "";
    this.recipeFilterValue = "";
    // let preIngredients = await this.$axios.$get("/allfoodcategories");
    // console.log("preIngredients ");
    // console.log(preIngredients);
    // this.ingredientList = preIngredients;

    let preCuisines = await this.$axios.$get("/allcuisines");
    console.log("preCuisines ");
    console.log(preCuisines);
    this.cuisineList = preCuisines;

    // this.ingredientList = ingredients

    let preCourses = await this.$axios.$get("/allcourses");
    console.log("preCourses ");
    console.log(preCourses);
    this.courseList = preCourses;

    let preUsers = await this.$axios.$get("/allusers");
    console.log("preUsers ");
    console.log(preUsers);
    this.userList = preUsers;
  },
};
</script>

<style scoped>
.margin {
  margin: 20px;
}
.img {
  border-radius: 8px;
  max-width: 350 !important;
  height: auto;
}
.avoidtextsquish {
  min-width: 200px;
}
.autoheight {
  height: auto;
}

@media only screen and (min-device-width: 1262px) and (max-device-width: 1740px) {
  .paddingscreensize {
    margin-right: 0px;
  }
}

.avoidtextsquishcity {
  min-width: 150px;
}
.v-input__slot {
  margin-bottom: 18px;
}
</style>