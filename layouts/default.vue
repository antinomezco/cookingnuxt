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
      <!-- <v-app-bar-nav-icon @click.stop="drawer = !drawer" /> -->
      <v-spacer />
      <v-btn to="/" router exact  plain> Recipe Search </v-btn>
      <v-btn v-if="$auth.loggedIn" to="/add_recipe" router exact  plain>
        Add Recipe
      </v-btn>
      <div v-if="user">
        <v-btn @click="adduser" plain>User settings</v-btn>
      </div>
      <v-spacer />
      <v-spacer />
      <v-spacer />
      <v-spacer />
      <v-spacer />
      <v-spacer />
      <v-spacer />
      <v-btn v-if="$auth.loggedIn" plain><a @click="$auth.logout()">Sign Out</a></v-btn>
      <v-btn v-else plain><a  @click="$auth.loginWith('auth0')">Sign In</a></v-btn>
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
