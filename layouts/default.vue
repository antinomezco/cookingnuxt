<template>
  <v-app dark>
    <!-- <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer> -->
    <v-app-bar :clipped-left="clipped" fixed app>
      <div class="text-center">
        <v-dialog overlay-opacity=".98" v-model="dialog" width="500">
          <template v-slot:activator="{ on, attrs }">
            <v-btn   dark icon plain v-bind="attrs" v-on="on">
              <v-icon dark> mdi-menu </v-icon>
            </v-btn>
          </template>
          <v-list-item>
            <v-list-item-content>
              <v-btn to="/" router exact plain> Recipe Search </v-btn>
            </v-list-item-content>
          </v-list-item>

          <v-list-item  v-if="$auth.loggedIn">
            <v-list-item-content>
              <v-btn to="/add_recipe" router exact plain> Add Recipe </v-btn>
            </v-list-item-content>
          </v-list-item>

          <v-list-item  v-if="user">
            <v-list-item-content>
                <v-btn @click="adduser" plain>User settings</v-btn>
            </v-list-item-content>
          </v-list-item>
          <v-list-item>
            <v-list-item-content>
              <v-btn v-if="$auth.loggedIn" plain
                ><a @click="$auth.logout()">Sign Out</a></v-btn
              >
              <v-btn v-else plain
                ><a @click="$auth.loginWith('auth0')">Sign In</a></v-btn
              >
            </v-list-item-content>
          </v-list-item>

          <!-- <v-card>
        <v-card-title class="headline grey lighten-2">
          Privacy Policy
        </v-card-title>

        <v-card-text>
          Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="primary"
            text
            @click="dialog = false"
          >
            I accept
          </v-btn>
        </v-card-actions>
      </v-card> -->
        </v-dialog>
      </div>
      <!-- <v-app-bar-nav-icon @click.stop="drawer = !drawer" /> -->
      <v-spacer />
      <v-btn to="/" class="d-none d-lg-block" router exact plain> Recipe Search </v-btn>
      <v-btn class="d-none d-lg-block" v-if="$auth.loggedIn" to="/add_recipe" router exact plain>
        Add Recipe
      </v-btn>
      <div class="d-none d-lg-block" v-if="user">
        <v-btn @click="adduser" plain>User settings</v-btn>
      </div>
      <v-spacer />
      <v-spacer />
      <v-spacer />
      <v-spacer />
      <v-spacer />
      <v-spacer />
      <v-spacer />
      <v-btn class="d-none d-lg-block" v-if="$auth.loggedIn" plain
        ><a @click="$auth.logout()">Sign Out</a></v-btn
      >
      <v-btn class="d-none d-lg-block" v-else plain
        ><a @click="$auth.loginWith('auth0')">Sign In</a></v-btn
      >
      <v-spacer />
    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt />
      </v-container>
    </v-main>
    <v-footer :absolute="!fixed" app>
      <span>C. Saldana &copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  computed: {
    user() {
      return this.$auth.user;
    },
    shaformation() {
      return this.CryptoJS.SHA1(this.user.sub);
    },
  },
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: true,
      items: [
        {
          title: "Recipe Search",
          to: "/",
        },
        {
          title: "Add Recipe",
          to: "/add_recipe",
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: "Vuetify.js",
    };
  },
  methods: {
    async adduser() {
      try {
        console.log("antes");
        console.log(this.CryptoJS.SHA1(this.$auth.user.sub).toString());
        await this.$axios.$post("/user_add/", {
          email: this.$auth.user.email,
          sub: this.CryptoJS.SHA1(this.$auth.user.sub).toString(),
        });
        // add {userid: this.$auth.identities.user_id}
        console.log("despues");
        console.log(this.formData);
        this.$router.push({
          name: "edit-user-sub",
          params: { sub: this.CryptoJS.SHA1(this.$auth.user.sub).toString() },
        });
      } catch (e) {
        // console.log("User already in database");
        this.$router.push({
          name: "edit-user-sub",
          params: { sub: this.CryptoJS.SHA1(this.$auth.user.sub) },
        });
        // setTimeout(this.$router.replace({ path: "/" }), 1000)
      }
    },
    // redirect() {
    //   this.$router.replace({ path: "/" });
    // },
  },
};
</script>
