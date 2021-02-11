<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <div class="text-center">
        <VueFuse
          :fuseOpts="options"
          :list="compostableData"
          :mapResults="false"
          :defaultAll="false"
          :placeholder="`Search Compost Pile`"
          @fuse-results="handleResults"
        />
        <div v-if="compostables.length > 0">
          <v-container fluid v-for="(compostable, i) in compostables" :key="i">
            <v-card tile elevation="2" class="pa-2">
              <v-card-title v-text="compostable.item.item"></v-card-title>
              <v-card-text>
                {{ compostable.item.compostable == 1 ? `` : `Not ` }}Compostable
                <v-card-text v-if="compostable.item.notes.length > 0">
                  <v-divider class="mx-4"></v-divider>
                  Notes: {{ compostable.item.notes }}
                </v-card-text>
                <div v-if="options.includeScore" class="">
                  <v-divider class="mx-4"></v-divider>
                  {{ compostable.score }}
                </div>
              </v-card-text>
            </v-card>
            <!-- <div class="">{{ compostable.item.item }}</div>
              <div class="">{{ compostable.item.notes }}</div>
              <div class="">{{ compostable.item.compostable }}</div>
              <div class="">{{ compostable.item.type }}</div>
              <div v-if="options.includeScore" class="">
                {{ compostable.score }}
              </div> -->
          </v-container>
        </div>
      </div>
    </v-col>
  </v-row>
</template>

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
      console.log("handling results, length=" + r.length);
      this.compostables = r;
    },
  },

  async asyncData({ req }) {
    const compostableData = await getCompostables();
    const includeScore = true;
    const compostables = [];
    console.log("compostable length " + compostableData.length);

    return { compostableData, includeScore, compostables };
  },
};
</script>
