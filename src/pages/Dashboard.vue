<template>
  <div>
    
    <p>Search: <input type="text" v-model="search" placeholder="Search movies by name.."/></p>
    <p class="popularTag">Popular Movies</p>
    <div class="row">
      <div class="col-lg-4" v-for="movie in filteredList" :key="movie.id">
        <card type="chart">
          <template slot="header">
            <h5 class="card-category">{{movie.original_title}}</h5>
          </template>
          <div class="chart-area">
              <div class="child">
                <p>Movie Name: {{movie.original_title}}</p>
                <img :src="'https://image.tmdb.org/t/p/original' + movie.backdrop_path">
                <p>Release Date: {{movie.release_date}}</p>
                <p>Vote: {{movie.vote_average}}</p>
                <b-btn @click="addDataToFavorites(movie)" variant="success">Add to Favorites</b-btn>
                <b-btn variant="success" :to="{ name: 'moviedetail', params: { Id: movie.id } }">Go to Movie Detail</b-btn>
              </div>
          </div>
        </card>
      </div>
    </div>
  </div>
</template>
<script>
  import LineChart from '@/components/Charts/LineChart';
  import BarChart from '@/components/Charts/BarChart';
  import * as chartConfigs from '@/components/Charts/config';
  import TaskList from './Dashboard/TaskList';
  import UserTable from './Dashboard/UserTable';
  import Swal from "sweetalert2";
  import config from '@/config';

  const axios = require('axios').default;

  export default {
    components: {
      LineChart,
      BarChart,
      TaskList,
      UserTable
    },
    data() {
      return {
        movies: [],
        search: null,
        favorites: [],
        isFavorite: false,
      }
    },
    computed: {
      enableRTL() {
        return this.$route.query.enableRTL;
      },
      isRTL() {
        return this.$rtl.isRTL;
      },
      bigLineChartCategories() {
        return this.$t('dashboard.chartCategories');
      },
      filteredList() {
        if (this.search != null && this.search != "") {
          return this.movies.filter(post => {
            return post.original_title.toLowerCase().includes(this.search.toLowerCase())
          })
        }
        else
          return this.movies;
    }
    },
    methods: {
      getPopularMovies() {
        axios.get("https://api.themoviedb.org/3/trending/movie/week?api_key=b6cf0cfb97b73f83a27cad8edd2634ba")
        .then(response => {
          for (var i = 0; i < response.data.results.length; i++) {
            this.movies.push(response.data.results[i]);
          }
        })
      },
      addDataToFavorites(value) {
        for (var i = 0; i < this.favorites.length; i++) {
          if (value.id != this.favorites[i].id) {
            axios.post("http://localhost:3000/favorites", {
                id: value.id,
                name: value.original_title,
                image: "https://image.tmdb.org/t/p/original" + value.backdrop_path,
                vote: value.vote_average,
                releaseDate: value.release_date,
              })
              .then(response => {
                Swal.fire("Successful!", "Operation is done!", "success");
              })
              .catch((error) => {
                Swal.fire("Error!", "This movie has already been added to favorites.", "error");
              })
          }
        }
      },
      getJsonData() {
        axios.get("http://localhost:3000/favorites")
        .then(response => {
          this.favorites.push(response.data);
        })
      }
    },
    mounted() {
      this.getPopularMovies();
      this.getJsonData();
      this.i18n = this.$i18n;
      if (this.enableRTL) {
        this.i18n.locale = 'ar';
        this.$rtl.enableRTL();
      }
    },
    beforeDestroy() {
      if (this.$rtl.isRTL) {
        this.i18n.locale = 'en';
        this.$rtl.disableRTL();
      }
    }
  };
</script>
<style>
.popularTag {
  font-weight: bold;
  font-size: 30px;
}
</style>
