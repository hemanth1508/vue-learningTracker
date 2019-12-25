<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <h1>Learning-Tracker</h1>
      </div>

      <v-spacer></v-spacer>
    </v-app-bar>

    <v-content>
      <HomePage />
    </v-content>
  </v-app>
</template>

<script>
import HomePage from "./components/HomePage";

export default {
  name: "App",

  components: {
    HomePage
  },

  data: () => ({
    //
  })
};
</script>
