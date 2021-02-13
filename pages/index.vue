<template>
  <v-container justify="center" align="center">
    <v-row justify="center" align="center" class="pa-2 mb-6">
      <VueFuse
        :fuseOpts="options"
        :list="compostableData"
        :mapResults="false"
        :defaultAll="false"
        :placeholder="`Search Compost Pile`"
        @fuse-results="handleResults"
        class="searchbox"
      />
    </v-row>
    <v-row v-if="compostables.length > 0" cols="12" sm="6" md="4">
      <v-col v-for="(compostable, i) in compostables" :key="i" class="col-md-6">
        <v-card
          tile
          elevation="2"
          outlined
          justify-center
          class="pa-2 rounded-lg"
        >
          <v-card-title v-text="compostable.item.item"></v-card-title>
          <v-card-text>
            <v-row>
              <v-col>
                <v-icon
                  large
                  v-if="compostable.item.compostable == 1"
                  color="green"
                  >mdi-check</v-icon
                >
                <v-icon large v-else color="red">mdi-close</v-icon>
                {{ compostable.item.compostable == 1 ? `` : `Not ` }}Compostable
              </v-col>
              <v-spacer></v-spacer>
            </v-row>


            <v-row v-if="compostable.item.notes.length > 0">
              <v-divider class="mx-4 mb-2"></v-divider>
              <v-card-text>
                Notes: {{ compostable.item.notes }}
              </v-card-text>
            </v-row>

            <div v-if="options.includeScore">
              <v-divider class="ma-4"></v-divider>
              <v-card-text>
                {{ compostable.score }}
              </v-card-text>
            </div>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    <v-row v-else justify="center" align="center"
      >Nothing found, try a different term to search for.</v-row
    >
  </v-container>
</template>

<style scoped>
.searchbox {
  color: white;
  box-sizing: border-box;
  border: 2px solid white;
  border-radius: 4px;
  padding: 5px;
  width: 60%;
}
</style>

<script>
import VueFuse from "vue-fuse";

const getCompostables = () =>
  import("~/data/compostablesData.json").then(
    (m) =>
      m.compostables ||
      m.default ||
      m.default.compostables ||
      m.compostables.default ||
      m
  );

export default {
  components: {
    VueFuse,
  },
  computed: {
    options() {
      return {
        keys: ["item"],
        includeScore: false,
        minMatchCharLength: 2,
        threshold: 0.25,
        isCaseSensitive: false,
      };
    },
  },
  methods: {
    handleResults(r) {
      this.compostables = r;
    },
  },

  async asyncData({ req }) {
    const compostableData = await getCompostables();
    const includeScore = true;
    const compostables = [];

    return { compostableData, includeScore, compostables };
  },
};
</script>
