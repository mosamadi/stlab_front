<template>
  <div class="home">
    <img alt="Vue logo" width="100" src="../assets/logo.png" />
    <BtnGoogle msg="Welcome to Your Vue.js App"></BtnGoogle>
  </div>
</template>

<script>
// @ is an alias to /src
import BtnGoogle from "@/components/BtnGoogle.vue";

export default {
  name: "HomeView",
  components: {
    BtnGoogle,
  },
};
</script>
