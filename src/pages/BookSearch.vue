<template>
  <div>
    <v-row>
      <v-col cols="6">
        <v-text-field label="本のタイトルを検索" v-model="keyword">
        </v-text-field>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="3">
        <v-btn color="primary" @click="search(keyword)">検索する</v-btn>
      </v-col>
      <v-col cols="3">
        <v-btn color="secondary" to="/">一覧に戻る</v-btn>
      </v-col>
    </v-row>
    <v-row dense>
      <v-col cols="4" v-for="(book, index) in searchresults" :key="book.index">
        <v-card>
          <v-img
            :src="book.image"
            class="white--text align-end"
            gradient="to bottom, rgba(0,0,0,.1), rgba(0,0,0,.5)"
            height="200px"
          >
            <v-card-title v-text="book.title">{{ book.title }}</v-card-title>
            {{ book.description }}
          </v-img>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn fab dark color="indigo" @click="addBookList(index)">
              <v-icon dark>mdi-plus</v-icon>
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
export default {
  name: "Bookserch",
  data() {
    return {
      keyword: "",
      searchresults: [],
    };
  },
  methods: {
      addBookList(index){
          this.$emit('add-book-list',this.searchresults[index])
      },
    async search(keyword) {
      this.searchresults = [];
      const baseUrl = "https://www.googleapis.com/books/v1/volumes?";
      const params = {
        q: `intitle:${keyword}`,
        maxResults: 40,
      };
      const queryParams = new URLSearchParams(params);
    //   console.log(baseUrl + queryParams);

      const response = await fetch(baseUrl + queryParams).then((response) =>
        response.json()
      );
    //   console.log(response.items);

      for (let book of response.items) {
        let title = book.volumeInfo.title;
        let img = book.volumeInfo.imageLinks;
        let description = book.volumeInfo.description;
        this.searchresults.push({
          title: title ? title : "",
          image: img ? img.thumbnail : "",
          description: description ? description.slice(0, 40) : "",
        });
      }
    },
  },
};
</script>

<style></style>
