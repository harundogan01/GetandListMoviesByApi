<template>
    <card class="mainCard">
        <p>Movie Name: {{movie[0].original_title}}</p>
        <img :src="'https://image.tmdb.org/t/p/original' + movie[0].backdrop_path" width="50%">
        <p>Description: {{movie[0].overview}}</p>
        <p>Popularity: {{movie[0].popularity}}</p>
        <p>Vote: {{movie[0].vote_average}}</p>
        <p>Vote Count: {{movie[0].vote_count}}</p>
        <p>Release Date: {{movie[0].release_date}}</p>
        <p>Homepage: <a :href="movie[0].homepage" target="_blank">Click Here...</a></p>
    </card>
</template>

<script>
  const axios = require('axios').default;
export default {
    data() {
        return {
            movie: []
        }
    },
    methods: {
        getMovieDetail() {
            axios.get("https://api.themoviedb.org/3/movie/" + this.$route.params.Id + "?api_key=b6cf0cfb97b73f83a27cad8edd2634ba")
            .then(response => {
                this.movie.push(response.data);
            })
        }
    },
    mounted() {
        this.getMovieDetail();
    }
}
</script>

<style scoped>
.mainCard {
    text-align: center;
    font-size: 20px;
    font-weight: bold;
};
</style>