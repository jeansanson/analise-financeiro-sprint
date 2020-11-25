<template>
  <v-container>
    <v-row dense>
      <v-col v-for="stock in stocks" :key="stock.title" :cols="3">
        <v-card>
          <v-card-title v-text="stock.title" class="justify-center"></v-card-title>
          <v-card-title v-text="stock.location" class="justify-center"></v-card-title>
          <v-card-title v-text="stock.value + '%'" class="justify-center"></v-card-title>
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
    results: null,
    stocks: [],
  }),
  created() {
    axios.get("https://api.hgbrasil.com/finance").then((resp) => {
      this.results = resp.data.results;
      const entries = Object.keys(resp.data.results.stocks);
      const stocks = [];
      console.log(resp.data.results);
      entries.forEach((element) => {
        const stock = resp.data.results.stocks[element];
        stocks.push({
          title: element,
          location: stock.location,
          value: stock.variation,
        });
      });
      this.stocks = stocks;
      console.log(this.stocks);
    });
  },
};
</script>

<style></style>
