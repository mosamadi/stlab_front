<template>
  <div class="home">
    <img alt="Vue logo" width="100" src="../assets/logo.png" />
    <h1>Validating Your Authentication...</h1>
    <p>Please Wait</p>
  </div>
</template>

<script>
// @ is an alias to /src

import router from "@/router";

export default {
  name: "GoogleOAuthView",
  data: function () {
    return {
      token: null,
    };
  },

  methods: {
    validateOAuth: async function () {
      console.log(this.$route.query);
      let params = new URLSearchParams(this.$route.query);

      const res = await fetch(
        "http://127.0.0.1:8000/api/oauth/google/callback?" + params.toString()
      );

      const data = await res.json();

      if (res.status === 200) {
        localStorage.setItem("token", data.authorization.token);
        router.push({ name: "about" });
      } else if (res.status === 422) {
        router.push({ name: "home" });
      }
    },
  },
  mounted() {
    this.validateOAuth();
  },
};
</script>
