<template>
  <div data-app>
    <div id="app">
      <v-app id="inspire">
        <v-card color="blue lighten-1" dark>
          <v-card-title class="headline blue lighten-3">
            Explore movies
          </v-card-title>
          <v-card-text>
            Explore hundreds of movies
          </v-card-text>
          <v-card-text>
            <v-autocomplete
              v-model="model"
              :items="items"
              :loading="isLoading"
              :search-input.sync="search"
              color="white"
              hide-no-data
              hide-selected
              item-text="Description"
              item-value="API"
              label="Public APIs"
              placeholder="Start typing to Search"
              prepend-icon="mdi-database-search"
              return-object
            ></v-autocomplete>
          </v-card-text>
          <v-divider></v-divider>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              :disabled="!model"
              color="grey darken-3"
              @click="model = null"
            >
              Clear
              <v-icon right>mdi-close-circle</v-icon>
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-app>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data: () => ({
    descriptionLimit: 60,
    entries: [],
    isLoading: false,
    model: null,
    search: null,
    items: [],
  }),

  computed: {},

  watch: {
    search(val) {
      if (!val) return;

      this.clearEntries();
      this.isLoading = true;
      this.fetchEntriesDebounced();
    },
  },

  methods: {
    clearEntries() {
      this.count = 0;
      this.entries = [];
    },
    fetchEntriesDebounced() {
      clearTimeout(this._searchTimerId);
      this._searchTimerId = setTimeout(() => {
        this.fetchEntries();
      }, 500); /* 500ms throttle */
    },
    async fetchEntries() {
      const { data } = await axios.get(
        `https://api.themoviedb.org/3/search/movie?api_key=263e31d1ad0c4defa8822787e614e716&language=en-US&query=${this.search}&page=1&include_adult=false`
      );

      const movies = data?.results.map((movie) => movie.original_title);
      this.items = movies;

      this.isLoading = false;
    },
  },
};
</script>