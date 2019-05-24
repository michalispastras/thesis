<template>
    <div class="container mt-4">
        <div class="row">
            <div class="col-3">
                <div class="form-group">
                    <label for="sel1">Select start year:</label>
                    <select class="form-control" id="sel1" v-model="startYear">
                        <option v-for="year in years" :value="year">{{ year }}</option>
                    </select>
                </div>
            </div>
            <div class="col-3">
                <div class="form-group">
                    <label for="year2">Select end year:</label>
                    <select class="form-control" id="year2" v-model="endYear">
                        <option v-for="year in years" :value="year">{{ year }}</option>
                    </select>
                </div>
            </div>
            <div class="col-6">
                <div class="form-group">
                    <label for="text">Search text:</label>
                    <input type="text" class="form-control" id="text" v-model="searchText">
                </div>
            </div>
        </div>
        <div class="row mt-4">
            <button class="btn btn-primary" style="margin: 0 auto;" v-on:click="search" :disabled="loading">
                SEARCH
            </button>
        </div>
        <div class="row mt-4" v-if="errorMessage">
            <p class="alert-danger" style="margin: 0 auto; width: 100%; text-align: center">{{ errorMessage }}</p>
        </div>
        <div class="row mt-4" v-if="infoMessage">
            <p class="alert-info" style="margin: 0 auto; width: 100%; text-align: center">{{ infoMessage }}</p>
        </div>
        <div class="row mt-5">
            <div class="spinner col-12 justify-content-center mb-5" v-if="loading" style="text-align: center">
                <i class="fas fa-spinner fa-spin" style="font-size: 400%;"></i>
            </div>
        </div>
        <div class="row mt-4" v-if="articles.length"><h3>{{ articles.length }} Articles Found:</h3></div>
        <div class="row mt-4">
            <div class="card-container mb-2 col-md-6 col-lg-4 col-sm-12" v-for="article in articles">
                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title">{{ article.title }}</h5>
                        <h6 class="card-subtitle mb-2 text-muted">{{ article.day }} {{ article.month }} {{ article.year }}</h6>
                        <p class="card-text">{{ article.articleTitle }}</p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from 'axios';

    const SEARCH_URL = 'https://eutils.ncbi.nlm.nih.gov/entrez/eutils/';

    export default {
        data() {
            return {
                loading: false,
                startYear: 2018,
                endYear: 2019,
                searchText: null,
                errorMessage: null,
                infoMessage: null,
                articles: []
            }
        },
        computed : {
            years () {
                const year = new Date().getFullYear();
                return Array.from({length: year - 2000}, (value, index) => year - index)
            }
        },
        methods: {
            search: function() {
                this.infoMessage = 'Searching...';
                this.loading = true;
                const searchText = this.searchText.trim().replace(/\s+/g, '+');
                const url = SEARCH_URL + 'esearch.fcgi?db=pubmed&term=' + searchText + '+AND+' + this.startYear + '+AND+' + this.endYear;
                this.articles = [];
                axios.get(url).then(res => {
                    let ids = [];
                    const parser = new DOMParser();
                    const xmlDoc = parser.parseFromString(res.data,"text/xml");

                    const idElements = xmlDoc.getElementsByTagName("Id");

                    for (let item of idElements) {
                        ids.push(item.innerHTML);
                    }

                    this.getArticlesByIds(ids);
                });
            },
            getArticlesByIds: function (ids) {
                this.infoMessage = 'Articles found! Transforming...';
                console.log(ids);

                const url = SEARCH_URL + 'efetch.fcgi?db=pubmed&id=' + ids.join() + '&rettype=xml';
                console.log(url);
                axios.get(url).then(res => {
                    this.parseAndDisplayArticles(res);
                });
            },
            parseAndDisplayArticles(res) {
                const parser = new DOMParser();
                const xmlDoc = parser.parseFromString(res.data,"text/xml");

                const articleElemets = xmlDoc.getElementsByTagName("PubmedArticle");
                this.articles = [];
                for (let articleEl of articleElemets) {
                    const pubDateEl = articleEl.getElementsByTagName('PubDate').item(0);
                    console.log(pubDateEl);
                    if(pubDateEl.getElementsByTagName('Year').item(0) && pubDateEl.getElementsByTagName('Month').item(0) && pubDateEl.getElementsByTagName('Day').item(0))
                        this.articles.push({
                            year: pubDateEl.getElementsByTagName('Year').item(0).innerHTML,
                            month: pubDateEl.getElementsByTagName('Month').item(0).innerHTML,
                            day: pubDateEl.getElementsByTagName('Day').item(0).innerHTML,
                            title: articleEl.getElementsByTagName('Title').item(0).innerHTML,
                            articleTitle: articleEl.getElementsByTagName('ArticleTitle').item(0).innerHTML
                        });
                }
                this.loading = false;
                this.infoMessage = null;
                console.log(this.articles);
            }
        },
        mounted() {
        }
    }
</script>

<style scoped>
    .btn {
        height: 60px;
        width: 200px;
    }
</style>
