<template>
    <div>You're logged in!</div>
</template>

<script>
export default {
    methods: {
        redirect() {
            this.$router.replace({ path: "/" });
        }
    },
    mounted() {
        setTimeout(this.redirect, 1000)
    }
}
</script>