<template>
  <v-container>
    <h1 class="mt-2 mb-2">Empresas</h1>
    <v-data-table
      :headers="headers"
      :items="stocks"
      :items-per-page="rowCountLimit"
      hide-default-footer
      class="elevation-1"
      @click:row="handleClick"
    ></v-data-table>
  </v-container>
</template>

<script>
const axios = require("axios");
const DomParser = require("dom-parser");
const parser = new DomParser();

export default {
  name: "Empresas",

  data: () => ({
    headers: [],
    stocks: [],
    rowCountLimit: 0,
  }),
  methods: {
    handleClick(args) {
     console.log(args)

    },
  },
  created() {
    const formData = new FormData();
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
      const columns = [];
      const rows = [];
      const rowCountLimit = 10;
      let rowIndex = 0;

      if (!tableElement) {
        console.warn("erro ao carregar dados");
        return;
      }

      tableElement.childNodes.forEach((thead) => {
        if (thead.nodeName === "thead") {
          thead.childNodes.forEach((headerRow) => {
            if (headerRow.nodeName === "tr") {
              headerRow.childNodes.forEach((headerContent) => {
                if (headerContent.nodeName === "th") {
                  columns.push(headerContent.childNodes[0].innerHTML);
                }
              });
            }
          });
        }
      });

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

      const gridHeaders = [];
      const field = "field";
      columns.forEach((column, index) => {
        gridHeaders.push({
          text: column,
          value: `${field}${index}`,
        });
      });

      const gridRows = [];
      rows.forEach((row) => {
        const gridRow = {};
        row.forEach((cell, index) => {
          gridRow[`${field}${index}`] = cell;
        });
        gridRows.push(gridRow);
      });

      this.headers = gridHeaders;
      this.stocks = gridRows;
      this.rowCountLimit = rowCountLimit;
    });
  },
};
</script>

<style></style>
