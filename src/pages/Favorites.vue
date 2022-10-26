<template>
  <div>
    <p class="popularTag">Favorites</p>
    <div class="row">
      <div class="col-lg-4" v-for="json in jsonList" :key="json.id">
        <card type="chart">
          <template slot="header">
            <h5 class="card-category">{{json.name}}</h5>
          </template>
          <div class="chart-area">
              <div class="child">
                <p>Movie Name: {{json.name}}</p>
                <img :src="json.image">
                <p>Release Date: {{json.releaseDate}}</p>
                <p>Vote: {{json.vote}}</p>
                <b-btn variant="success" :to="{ name: 'moviedetail', params: { Id: json.id } }">Go to Movie Detail</b-btn>
              </div>
          </div>
        </card>
      </div>
    </div>
  </div>
</template>

<script>
const axios = require('axios').default;
export default {
  data() {
    return {
      jsonList: []
    }
  },
  methods: {
    getJsonData() {
        axios.get("http://localhost:3000/favorites")
        .then(response => {
          response.data.forEach(value => {
            this.jsonList.push(value);
          })
          console.log("JsonList: ", this.jsonList);
        })
      }
  },
  mounted() {
    this.getJsonData();
  }
}
</script>