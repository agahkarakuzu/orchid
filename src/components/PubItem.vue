<template>
  <b-list-group v-if="pub">
    <b-row align-h="between">
    <b-col cols="10">
    <div class="d-flex justify-content-between">
      <a class="mb-1" :href="pub['url']['value']" target="_blank">
        {{ pub['title']['title']['value'] }}
      </a>
    </div>
    <div>
    <p v-if="pub['journal-title']" class="mb-1">
      <small>{{ pub['journal-title']['value'] }} ({{ pub['publication-date']['year']['value'] }})</small>
    </p>
    <p v-else class="mb-1">
      <small>Preprint ({{ pub['publication-date']['year']['value'] }})</small>
    </p>
    </div>
    </b-col>
    <b-col cols="2" align-self="center">
      <div class="d-flex float-right">
      <span class="__dimensions_badge_embed__"
        :data-doi="doi"
        data-style="small_circle"
        data-hide-zero-citations="false">
      </span>
      </div>
    </b-col>
    </b-row>
  </b-list-group>
</template>

<script>

export default {
  name: 'Bloom',
  props: {
    doi: { type: String },
    pub: { type: Object },
    authorList: {type: Array},
    showAuthors: { type: Boolean }, // Accept showAuthors prop
  },
  data() {
    return {
      works: [],
      loading: true,
    };
  },
  mounted() {
    //this.getCrossref(this.doi);
    let dimensionScript = document.createElement('script')
    dimensionScript.setAttribute('src', 'https://badge.dimensions.ai/badge.js')
    document.head.appendChild(dimensionScript)
  },
  methods: {
    toggleAuthors() { // New method to toggle author visibility
      this.showAuthors = !this.showAuthors;
    },
    styleAuthor(author) {
      var style = {}; // Replace with your own family name !
      if (author['family'] == 'Karakuzu') {
        style.color = 'black',
        style.textDecoration = 'underline'
      }
      return style;
    }
  },
};
</script>

<style>
</style>
