<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <h1>Financial Project</h1>
      </div>

      <v-spacer></v-spacer>
    </v-app-bar>

    <v-main>
      <v-tabs fixed-tabs background-color="indigo" dark v-model="tab">
        <v-tab>
          Empresas
        </v-tab>
        <v-tab>
          Notícias
        </v-tab>
        <v-tab>
          Mercados
        </v-tab>
      </v-tabs>

      <v-tabs-items v-model="tab">
        <v-tab-item>
          <Empresas />
        </v-tab-item>
        <v-tab-item>
          <Noticias />
        </v-tab-item>
        <v-tab-item>
          <Mercados />
        </v-tab-item>
      </v-tabs-items>
    </v-main>
  </v-app>
</template>

<script>
import Empresas from "./components/Empresas";
import Noticias from "./components/Noticias";
import Mercados from "./components/Mercados";

export default {
  name: "App",

  components: {
    Empresas,
    Noticias,
    Mercados,
  },

  data: () => ({
    tab: null,
  }),
};
</script>
