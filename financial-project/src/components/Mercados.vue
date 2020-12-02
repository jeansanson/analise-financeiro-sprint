<template>
  <v-container>
    <h2 class="text-center mt-2 mb-2">
      Índices de bolsas de valores pelo mundo
    </h2>
    <v-row dense>
      <v-col
        v-for="stock in stocks"
        :key="stock.title"
        :cols="cols"
        class="text-center"
      >
        <v-card>
          <v-card-title
            v-text="stock.title"
            class="justify-center"
          ></v-card-title>
          <v-card-title
            v-text="stock.location"
            class="justify-center"
            style="word-break: keep-all"
          ></v-card-title>
          <v-card-title
            v-text="stock.value + '%'"
            class="justify-center"
          ></v-card-title>
        </v-card>
      </v-col>
    </v-row>
    <h2 class="text-center mt-2 mb-2">
      Cotação das principais moedas para o real
    </h2>
    <v-row dense>
      <v-col v-for="currency in currencies" :key="currency.title" :cols="cols">
        <v-card>
          <v-card-title
            v-text="currency.title + '/BRL'"
            class="justify-center"
          ></v-card-title>
          <v-card-title
            v-text="'R$' + currency.value"
            class="justify-center"
          ></v-card-title>
          <v-card-title
            v-text="currency.variation + '%'"
            class="justify-center"
          ></v-card-title>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
const axios = require("axios");

export default {
  name: "Mercados",

  data: () => ({
    stocks: [],
    currencies: [],
  }),
  computed: {
    cols() {
      switch (this.$vuetify.breakpoint.name) {
        case "xs":
          return 6;
        case "sm":
          return 6;
        case "md":
          return 4;
        case "lg":
          return 3;
        case "xl":
          return 3;
        default:
          return 3;
      }
    },
  },
  created() {
    axios.get("https://api.hgbrasil.com/finance").then((resp) => {
      const stockEntries = Object.keys(resp.data.results.stocks);
      const currenciesEntries = Object.keys(resp.data.results.currencies);
      const stocks = [];
      const currencies = [];

      stockEntries.forEach((element) => {
        const stock = resp.data.results.stocks[element];
        stocks.push({
          title: element,
          location: stock.location,
          value: stock.variation,
        });
      });
      this.stocks = stocks;
      
      currenciesEntries.forEach((element) => {
        const currency = resp.data.results.currencies[element];
        if (element !== "source") {
          currencies.push({
            title: element,
            value: String(currency.buy).replace(".", ","),
            variation: currency.variation,
          });
        }
      });
      this.currencies = currencies;
    });
  },
};
</script>

<style></style>
