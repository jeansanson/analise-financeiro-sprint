<template>
  <v-container>
    <h1 class="mt-2 mb-2">Notícias</h1>
    <v-row dense>
      <v-col
        v-for="article in news"
        :key="article.title"
        :cols="12"
        class="text-left"
      >
        <a @click="goToWebsite(article.url)">
          <v-card>
            <v-card-title v-text="article.title"></v-card-title>
            <v-card-text v-text="article.description"> </v-card-text>
            <v-card-actions class="ml-2" v-text="article.source.name + ' - ' + convertDate(article.publishedAt)"></v-card-actions>
          </v-card>
        </a>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
const axios = require("axios");

export default {
  name: "Noticias",

  data: () => ({
    news: [],
  }),

  created() {
    var url =
      "http://newsapi.org/v2/everything?" +
      "q=MGLU3&" +
      "from=2020-12-01&" +
      "to=2020-12-08&" +
      "sortBy=popularity&" +
      "apiKey=26757ee92978440a904198680e941748";
    axios.get(url).then((resp) => {
      this.news = resp.data.articles;
      console.log(this.news);
    });
  },
  methods: {
    goToWebsite(url) {
      window.open(url);
    },
    convertDate(string){
      const date = new Date(string);
      return date.toLocaleDateString('pt-BR', {dateStyle: "long"});
    }
  },
};
</script>

<style></style>
