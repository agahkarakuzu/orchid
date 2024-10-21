# Orchid

[![Netlify Status](https://api.netlify.com/api/v1/badges/860bc953-499d-4b41-80ac-1e66d2e3e0fb/deploy-status)](https://app.netlify.com/sites/agkarpubs/deploys)

One of the most annoying tasks in academia is keeping your academic record up to date, creating lists in a specific style requested by funding agencies, etc.

Orchid automates this process.

## How it works

> This is the second iteration of [bloom](https://github.com/emdupre/bloom). 

Your academic record is automatically populated from your [ORCID](https://orcid.org/about). That's it! 

* Update `src/components/data/author-info.json` with your information.
* Deploy the site to Netlify.
* Updates to your ORCID profile will be reflected on the site automatically & dynamically.

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For detailed explanation on how things work,
checkout the [guide](http://vuejs-templates.github.io/webpack/) and
[docs for vue-loader](http://vuejs.github.io/vue-loader).