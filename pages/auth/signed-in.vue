<template>
  <div>You're logged in!</div>
</template>

<script>
export default {
  data(){
    // return {
    //   formData: {
    //     email: this.$auth.user.email, 
    //     sub: this.$auth.user.sub
    //   }
    // }
  },
  methods: {
    // async postRecipe() {
    //   try {
    //     console.log("antes");
    //     console.log(this.formData)
    //     await this.$axios.$post("/user_add/", this.formData);
    //     // add {userid: this.$auth.identities.user_id}
    //     console.log("despues");
    //     console.log(this.formData)
    //     setTimeout(this.$router.replace({ name: "edit-user-sub" }), 1000)
    //   } 
    //   catch(e) {
    //     console.log('User already in database')
    //     setTimeout(this.$router.replace({ path: "/" }), 1000)
    //   }
    // },
    redirect() {
      this.$router.replace({ path: "/" });
    },
  },
  mounted() {
    setTimeout(this.redirect, 1000);
  },
};
</script>