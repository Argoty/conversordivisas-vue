<template>
  <v-app>
    <v-app-bar app color="deep-purple darken-3" dark>
      <div class="d-flex align-center">
        <v-img
          src="./assets/logomoneda.png"
          alt="Climaexact Logo"
          class="shrink mr-2"
          contain
          transition="scale-transition"
          width="60"
        />

        <h1>ConversorGO</h1>
        <v-icon x-large class="ml-2">mdi-cash-100</v-icon>
      </div>

      <v-spacer></v-spacer>

      <v-btn href="https://www.exchangerate-api.com/" target="_blank" text>
        <span class="mr-2">ExchangeRate-API</span>
        <v-icon>mdi-cash-multiple</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <ConversorApp />
    </v-main>

    <Footer />
  </v-app>
</template>

<script>
import ConversorApp from "./components/ConversorApp";
import Footer from "./components/Footer";

export default {
  name: "App",

  components: {
    ConversorApp,
    Footer,
  },

  data: () => ({
    //
  }),
};
</script>
