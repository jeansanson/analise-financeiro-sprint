<template>
  <v-container>
    <h1 class="mt-2 mb-2">Notícias</h1>
    <v-row dense>
      <v-col v-for="article in news" :key="article.title" :cols="12" class="text-left">
        <a @click="goToWebsite(article.url)">
          <v-card>
            <v-card-title v-text="article.title"></v-card-title>
            <v-card-text v-text="article.description"> </v-card-text>
            <v-card-actions
              class="ml-2"
              v-text="article.source.name + ' - ' + convertDate(article.publishedAt)"
            ></v-card-actions>
          </v-card>
        </a>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
const axios = require("axios");
const DomParser = require("dom-parser");
const parser = new DomParser();

export default {
  name: "Noticias",

  data: () => ({
    news: [],
  }),

  created() {
    this.loadTopCompaniesNews().then((tags) => {
      if (!tags) return;
      setTimeout(() => {
        this.loadNews(tags);
      }, 1500);
    });
  },

  methods: {
    goToWebsite(url) {
      window.open(url);
    },
    convertDate(string) {
      const date = new Date(string);
      return date.toLocaleDateString("pt-BR", { dateStyle: "long" });
    },
    getUrl(tag) {
      const actualDate = new Date();
      const lastMonth = new Date(new Date().setMonth(actualDate.getMonth() - 1));
      const actualDateStr = actualDate.toISOString().split("T")[0];
      const lastMonthStr = lastMonth.toISOString().split("T")[0];
      return (
        "http://newsapi.org/v2/everything?" +
        `q=${tag}&` +
        `from=${lastMonthStr}&` +
        `to=${actualDateStr}&` +
        "sortBy=popularity&" +
        "apiKey=26757ee92978440a904198680e941748"
      );
    },
    loadNews(tags) {
      let news = [];
      tags.forEach((tag, index) => {
        const url = this.getUrl(tag);
        axios.get(url).then((resp) => {
          const articles = resp.data.articles;
          articles.forEach((art) => {
            art.title = `${tag} - ${art.title}`;
          });
          news = news.concat(articles);
          if (index === tags.length - 1) {
            this.news = news.filter(
              (v, i, a) =>
                a.findIndex((t) => JSON.stringify(t) === JSON.stringify(v)) === i
            );
          }
        });
      });
    },
    async loadTopCompaniesNews() {
      const formData = new FormData();
      const tags = [];
      formData.append("firma_ebit_min", "0");
      formData.append("firma_ebitda_min", "0");
      formData.append("liq_min", "200000");
      formData.append("negociada", "ON");
      formData.append("ordem", "9");
      axios({
        method: "post",
        url: "http://fundamentus.com.br/resultado.php",
        data: formData,
        headers: { "Content-Type": "multipart/form-data" },
      }).then((resp) => {
        const response = resp.data;
        const doc = parser.parseFromString(response, "text/html");
        const tableElement = doc.getElementById("resultado");
        const rows = [];
        const rowCountLimit = 10;
        let rowIndex = 0;

        if (!tableElement) {
          console.warn("erro ao carregar dados");
          return;
        }

        tableElement.childNodes.forEach((tbody) => {
          if (tbody.nodeName === "tbody") {
            tbody.childNodes.forEach((trow) => {
              if (trow.nodeName === "tr") {
                trow.childNodes.forEach((td) => {
                  if (td.nodeName === "td") {
                    if (rowIndex + 1 <= rowCountLimit) {
                      const html = td.innerHTML;
                      const row = rows[rowIndex];
                      let rowContent = null;
                      if (row === undefined) rows[rowIndex] = [];

                      if (html.includes("title=")) {
                        rowContent = html.split('">')[2].split("</")[0];
                      } else {
                        rowContent = html;
                      }

                      rows[rowIndex].push(rowContent);
                    }
                  }
                });
                rowIndex++;
              }
            });
          }
        });

        rows.forEach((row) => {
          row.forEach((cell, index) => {
            if (index === 0) {
              tags.push(cell);
            }
          });
        });
      });
      return tags;
    },
  },
};
</script>

<style></style>
