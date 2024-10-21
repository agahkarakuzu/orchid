<template>
  <div id="Pubs">
    <div v-if="loading" class="d-flex justify-content-center mb-3">
      <b-spinner variant="primary"></b-spinner>
    </div>
    <div v-else class="container-fluid pub-content">
      <b-row align="left">
        <b-col cols="8">
          <h4>
            {{ typeCounts.journal }} Journal Articles, 
            {{ typeCounts.conference }} Conference Papers, 
            {{ typeCounts.preprint }} Preprints
          </h4>
        </b-col>
        <b-col cols="4" class="d-flex justify-content-end align-items-center">
          <v-btn @click="downloadApaMarkdown"> <v-icon>mdi-language-markdown</v-icon> Download Citation List
          </v-btn>
      <b-dropdown id="dropdown-right" right :text="selectedStyle" variant="warning" class="m-1">
        <b-dropdown-item @click="selectStyle('apa')">APA</b-dropdown-item>
        <b-dropdown-item @click="selectStyle('ieee')">IEEE</b-dropdown-item>
        <b-dropdown-item @click="selectStyle('nature')">Nature</b-dropdown-item>
        <b-dropdown-item @click="selectStyle('modern-language-association')">MLA</b-dropdown-item>
        <b-dropdown-item @click="selectStyle('chicago-author-date')">Chicago</b-dropdown-item>
        <b-dropdown-item @click="selectStyle('vancouver')">Vancouver</b-dropdown-item>
        <b-dropdown-item @click="selectStyle('harvard')">Harvard</b-dropdown-item>
      </b-dropdown>
        </b-col>
      </b-row>
      <b-progress
      v-if="downloadingApa"
      :value="downloadProgress"
      :max="100"
      height="20px"
      animated
      class="mt-2">
      <b-progress-bar :value="downloadProgress" variant="warning" striped="striped">
        <strong>{{ Math.ceil(downloadProgress) }}%</strong>
      </b-progress-bar>
    </b-progress>
      <b-list-group>
        <b-list-group-item v-for="(work, index) in works" :key="index">
          <b-row>
            <b-col cols="1"  class="d-flex justify-content-center align-items-center">
              <div class="text-center">
                <v-btn
                  class="mx-2"
                  fab
                  small
                  label="Details"
                  v-bind="attrs"
                  v-on="on"
                  @click="fetchAuthorList(index, getDoi(work))"
                  style="background-color: ghostwhite;"
                >
                  <v-icon>{{ authorLists[index] ? 'mdi-minus' : 'mdi-plus' }}</v-icon>
                </v-btn>
              </div>
            </b-col>
            <b-col cols="11">
              <PubItem
                :pub="work['work-summary'][0]"
                :doi="getDoi(work)"
              ></PubItem>
              <div class="d-flex align-items-center">
                <a :href="`https://doi.org/${getDoi(work)}`" target="_blank" @click.prevent="downloadBibCitation(getDoi(work))" class="mr-2">
                  <img :src="`https://img.shields.io/badge/Cite-BibTeX-lightgray?logo=doi`" alt="Cite this DOI">
                </a>
                <span><img :src="`https://img.shields.io/badge/${encodeDoiForBadge(work['work-summary'][0]['type'])}-${encodeColor(work['work-summary'][0]['type'])}?logo=orcid`"></span>
              </div>
              <br>
              <div v-if="fetchingIndex === index" class="d-flex justify-content-center mt-2">
                <b-spinner variant="warning"></b-spinner>
              </div>
              <v-expansion-panels v-model="panel" v-if="authorLists[index] && !fetchingIndex" multiple>
                <v-expansion-panel :value="0" style="margin-top: 10px;">
                  <v-expansion-panel-header>Authors</v-expansion-panel-header>
                  <v-expansion-panel-content>
                    <p v-html="formatAuthors(authorLists[index])"></p>
                  </v-expansion-panel-content>
                </v-expansion-panel>
                <v-expansion-panel>
                  <v-expansion-panel-header>Abstract</v-expansion-panel-header>
                  <v-expansion-panel-content v-if="abstracts[index]">
                    <p v-html="abstracts[index]"></p>
                  </v-expansion-panel-content>
                </v-expansion-panel>
              </v-expansion-panels>
            </b-col>
          </b-row>
        </b-list-group-item>
      </b-list-group>
    </div>
  </div>
</template>

<script>
import Vue from 'vue';
import PubItem from './PubItem';
import authorInfo from './data/author-info.json';

export default {
  name: 'Bloom',
  components: {
    PubItem,
  },
  data() {
    return {
      works: [],
      authorLists: [],
      abstracts: [],
      loading: true,
      downloadProgress: 0,
      selectedStyle: 'nature',
      fetchingIndex: null,
      downloadingApa: false,
      panel: [], 
      authorInfo: authorInfo,
    };
  },
  mounted() {
    // this.getOrcid('0000-0001-7283-271X');
    this.getOrcid(authorInfo.orcid);
  },
  methods: {
    selectStyle(style) {
      this.selectedStyle = style;
    },
    async fetchAuthorList(index, doi) {
      if (this.authorLists[index]) {
        this.$set(this.authorLists, index, null);
        this.$set(this.abstracts, index, null);
        this.panel = []; // Close all panels when re-fetching
        return;
      }

      this.fetchingIndex = index;
      const options = {
        method: 'GET',
        headers: new Headers({ 'accept': 'application/json' }),
      };

      try {
        const response = await fetch(`https://api.crossref.org/works/${doi}`, options);
        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }
        const data = await response.json();

        const authors = (data.message && data.message.author) ? data.message.author : [];
        const abstract = (data.message && data.message.abstract) ? data.message.abstract : null;

        this.$set(this.authorLists, index, authors);
        this.$set(this.abstracts, index, abstract);
        this.panel = [0]; // Open the Authors panel by default
      } catch (error) {
        console.error('Error fetching data:', error);
        this.$set(this.authorLists, index, []);
        this.$set(this.abstracts, index, null);
      } finally {
        this.fetchingIndex = null;
      }
    },
    async getOrcid(orcid) {
      const options = {
        method: 'GET',
        headers: new Headers({ 'accept': 'application/json' }),
      };
      try {
        const response = await fetch(`https://pub.orcid.org/v3.0/${orcid}/works`, options);
        const data = await response.json();
        this.works = data.group;
        this.loading = false;
      } catch (error) {
        console.error('Error fetching ORCID works:', error);
        this.loading = false;
      }
    },
    async downloadApaMarkdown() {
      this.downloadingApa = true;
      this.downloadProgress = 0;
      let markdownContent = '# Publications\n\n';

      const headers = {
        Accept: `text/x-bibliography; style=${this.selectedStyle}`
      };
      const totalWorks = this.works.length;
      let processedWorks = 0;

      for (const type of ['journal-article', 'conference-paper', 'preprint']) {
        const typeLabel = type === 'journal-article' ? 'Journal Articles' : type === 'conference-paper' ? 'Conference Papers' : 'Preprints';
        let typeSection = `## ${typeLabel}\n\n`;

        const filteredWorks = this.works.filter(work => work['work-summary'][0]['type'] === type);
        if (filteredWorks.length === 0) continue;
        
        let citationNumber = 1;

        for (const work of filteredWorks) {
          const doi = this.getDoi(work);
          try {
            const url = `https://doi.org/${doi}`;
            console.log(url);
            const response = await fetch(url, { headers });
            if (!response.ok) {
              throw new Error(`HTTP error! Status: ${response.status}`);
            }
            const citation = await response.text();
            typeSection += `${citationNumber}. ${citation.replace(/^\[?\d+\]?\.\s*/, "")}\n`;
            citationNumber++;
          } catch (error) {
            console.error(`Error fetching citation for ${doi}:`, error);
          }

          processedWorks++;
          this.downloadProgress = (processedWorks / totalWorks) * 100;
        }
        markdownContent += `${typeSection}\n`;
      }

      const blob = new Blob([markdownContent], { type: 'text/markdown' });
      const url = window.URL.createObjectURL(blob);
      const link = document.createElement('a');
      link.href = url;
      link.download = `${authorInfo.name}-${authorInfo.surname}-publications.md`;
      link.click();
      window.URL.revokeObjectURL(url);
      this.downloadingApa = false; 
    },
    formatAuthors(authors) {
      const boldAuthors = [authorInfo.surname];
      return authors
        .map(author => {
          const name = `${author.given} ${author.family}`;
          return boldAuthors.includes(author.family) ? `<span class="bold-author">${name}</span>` : name;
        })
        .join(', ');
    },
    getDoi(work) {
      return work['work-summary'][0]['url']['value'].replace(/^https?:\/\/(dx\.)?doi\.org\//, '');
    },
    encodeDoiForBadge(doi) {
      switch (doi) {
        case 'journal-article':
          return 'Article';
        case 'conference-paper':
          return 'Conference Paper';
        case 'preprint':
          return 'Preprint';
        default:
          return 'Other';
      }
    },
    encodeColor(inp) {
      switch (inp) {
        case 'journal-article':
          return '005149';
        case 'conference-paper':
          return 'blue';
        case 'preprint':
          return '2B2B2B';
        default:
          return 'gray';
      }
    },
    async downloadBibCitation(doi) {
      const options = {
        method: 'GET',
        headers: new Headers({ 'accept': 'application/x-bibtex' }),
      };
      try {
        const response = await fetch(`https://api.crossref.org/works/${doi}/transform/application/x-bibtex`, options);
        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }
        const bibText = await response.text();

        const blob = new Blob([bibText], { type: 'text/plain' });
        const url = window.URL.createObjectURL(blob);
        const link = document.createElement('a');
        link.href = url;
        link.download = `${doi}.bib`;
        link.click();
        window.URL.revokeObjectURL(url);
      } catch (error) {
        console.error('Error downloading .bib citation:', error);
      }
    },
  },
  computed: {
    typeCounts() {
      const counts = {
        journal: 0,
        conference: 0,
        preprint: 0,
        other: 0
      };

      this.works.forEach(work => {
        const type = work['work-summary'][0]['type'];
        switch (type) {
          case 'journal-article':
            counts.journal++;
            break;
          case 'conference-paper':
            counts.conference++;
            break;
          case 'preprint':
            counts.preprint++;
            break;
          default:
            counts.other++;
        }
      });

      return counts;
    }
  },
};
</script>

<style>
.pub-content {
  background: transparent;
  position: relative;
  margin-left: 10px;
  margin-bottom: 10px;
  padding: 40px 60px 0 0;
}
.bold-author {
  font-weight: bold;
  text-decoration: underline;
}
</style>
