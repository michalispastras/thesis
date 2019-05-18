<template>
    <div class="container">
        <div class="row mt-5">
            <div class="spinner col-12 justify-content-center mb-5" v-if="loading" style="text-align: center">
                <i class="fas fa-spinner fa-spin" style="font-size: 400%;"></i>
            </div>
            <div class="card-container mb-2 col-md-6 col-lg-4 col-sm-12" v-for="article in articles">
                <div class="card">
                    <div class="card-body">
                        <h5 class="card-title">{{ article.title }}</h5>
                        <h6 class="card-subtitle mb-2 text-muted">{{ article.author }}</h6>
                        <p class="card-text">{{ article.description }}</p>
                        <a :href="article.url" target="_blank" class="card-link">Read more</a>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from 'axios'

    const API_KEY = 'f92d0baf3d53487085a2295201ed81a8';

    export default {
        data() {
            return {
                articles: [] = [],
                loading : true
            }
        },
        mounted() {
            axios.get('https://newsapi.org/v2/top-headlines?country=us&apiKey=' + API_KEY).then(res => {
                this.articles = res.data.articles;
                this.loading = false;
                console.log(this.articles);
            });
        }
    }
</script>

<style>
    .btn {
        height: 60px;
        width: 200px;
    }
</style>
