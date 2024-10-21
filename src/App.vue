<template>
  <div id="app">
    <b-navbar toggleable="md" type="light" variant="light">
      <b-navbar-toggle target="nav_collapse"></b-navbar-toggle>
      <v-icon>mdi-book-open-page-variant</v-icon> &nbsp;&nbsp;
      <b-navbar-brand to="/" exact> <a v-if="authorInfo.orcid" :href="'https://orcid.org/' + authorInfo.orcid" target="_blank"><img alt="ORCID logo" src="https://info.orcid.org/wp-content/uploads/2019/11/orcid_16x16.png" width="16" height="16" /> {{ authorInfo.orcid }} </a> &nbsp;&nbsp; {{ authorInfo.name }} {{ authorInfo.surname }}
      </b-navbar-brand>

      <b-collapse is-nav id="nav_collapse">
        <!--  Here are links to different routes  -->
        <b-navbar-nav class="ml-auto">
          <b-nav-item :href="authorInfo.website"><v-icon large> mdi-home</v-icon></b-nav-item>
          <!--  <b-nav-item to="/pubs">Publications</b-nav-item> -->
          <!-- <b-nav-item to="/cv">CV</b-nav-item> -->
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>

    <div>
        <router-view></router-view>
    </div>

    <footer>
        <div class="fluid-container footer">
          <p class="text-center">
            <span class="copy-left"><a href="//copyleft.org/">©</a></span>
            {{ authorInfo.name }} {{ authorInfo.surname }}
            <a v-if="authorInfo.orcid" :href="'https://orcid.org/' + authorInfo.orcid" target="_blank">
              <img alt="ORCID logo" src="https://info.orcid.org/wp-content/uploads/2019/11/orcid_16x16.png" width="16" height="16" />
              {{ authorInfo.orcid }}
            </a>
          </p>
        </div>
    </footer>
  </div>
</template>

<script>
import Vue from 'vue';
import BootstrapVue from 'bootstrap-vue';
Vue.use(BootstrapVue);
import authorInfo from '@/components/data/author-info.json';

export default {
  name: 'app',
    data() {
    return {
      authorInfo
    };
  }
}
</script>

<style>
#app {
  /* font-family: 'Avenir', Helvetica, Arial, sans-serif; */
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

@import url('https://fonts.googleapis.com/css?family=Open+Sans|Ikota+Web');

/* ===========
basics
============= */
body {
    width: 100%;
    height: 100%;
    font-family: 'Open Sans', sans-serif;
    font-weight: 400;
    color: #666;
    background: #fff;
    font-size: 16.5px;
    line-height: 1.6em;
}

html {
    width: 100%;
    height: 100%;
}

label,
input,
button,
select,
textarea {
  font-family: 'Open Sans', sans-serif;
  color: #6a6e75;
  font-size: inherit;
  line-height: inherit;
}

.float-left{
    float: left;
}

.float-right{
    float: right;
}

/* ===========
typography
============= */

h1 {
    font-size: 38px;
}
h2 {
    font-size: 36px;
}
h3 {
    font-size: 32px;
}
h4 {
    font-size: 28px;
}
h5 {
    font-size: 24px;
}
h6 {
    font-size: 20px;
}

h1, h2,
h3, h4,
h5, h6 {
    margin: 0 0 40px;
    font-family: 'Ikota Web', sans-serif;
    font-weight: 100;
    color: #444;
    line-height: 1.1em;
}

p {
    margin: 0 0 20px;
}

a {
    color: #666;
    -webkit-transition: all .2s ease-in-out;
    -moz-transition: all .2s ease-in-out;
    transition: all .2s ease-in-out;
}

a:hover,
a:focus {
    text-decoration: none;
}

/* ===========
misc elements
============= */
br {
    line-height: 5px;
}

hr {
  border-top: 1px solid #fafafa;
}

hr.bold {
  border-top: 3px solid #eee;
}

.copy-left {
    display: inline-block;
    text-align: right;
    margin: 0px;
    -moz-transform: scaleX(-1);
    -o-transform: scaleX(-1);
    -webkit-transform: scaleX(-1);
    transform: scaleX(-1);
    filter: FlipH;
    -ms-filter: “FlipH”; }


</style>
