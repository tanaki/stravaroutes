<template>

  <div class="loggedout" v-if="(!athlete && !code)">
    <h3>Strava Routes</h3>
    <a :href="connectionPage">
      <img src="./assets/btn_strava_connectwith_orange.svg" />
    </a>
  </div>

  <div v-if="athlete && !routes">Loading</div>

  <div v-if="routes" class="">

    <List :routes="routes" />
  </div>
</template>

<script>

import List from './components/List.vue'

export default {
  name: 'App',

  data () {

    return {
      athlete: null,
      code: null,
      connectionPage: "https://www.strava.com/oauth/authorize?client_id=242&response_type=code&redirect_uri=http://nicolaspigelet.com/strava/routes&approval_prompt=force",
      routes: null,
      token: null
    }
  },

  components: {
    List
  },

  mounted () {

    var self = this;
    window.location.search.split('&').forEach(( s ) => {
      var splitted = s.split('=')
      if ( splitted[0] == 'code' ) {
        self.code = splitted[1];
        self.fetchToken()

        // self.token = splitted[1];
        // self.fetchAthlete();
      }
    });
  },

  methods: {

    fetchToken () {

      var self = this;

      const requestOptions = {
        method: "POST",
        headers: { 
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          client_id: 242,
          client_secret: "1156bdd01e5679547af4ac46bce0722964704f06",
          code: self.code,
          grant_type: "authorization_code"
        })
      };

      fetch( "https://www.strava.com/oauth/token", requestOptions)
        .then(response => response.json())
        .then(data => {

          if ( data.errors ) {

            console.log("error");

          } else {

            self.token = data.access_token
            self.fetchAthlete();
          }

        });
    },
    
    fetchAthlete () {

      var self = this;

      const requestOptions = {
        method: "GET",
        headers: { 
          "Content-Type": "application/json",
          "Authorization": "Bearer " + this.token
        }
      };

      fetch( "https://www.strava.com/api/v3/athlete", requestOptions)
        .then(response => response.json())
        .then(data => {

          if ( data.errors ) {

            console.log("error");

          } else {

            self.athlete = data
            self.fetchRoutes();
          }

        });

    },

    fetchRoutes () {

      var self = this;

      const requestOptions = {
        method: "GET",
        headers: { 
          "Content-Type": "application/json",
          "Authorization": "Bearer " + this.token
        }
      };

      fetch( "https://www.strava.com/api/v3/athletes/" + this.athlete.id + "/routes?per_page=200", requestOptions)
        .then(response => response.json())
        .then(data => {

          if ( data.errors ) {

            console.log("error");

          } else {

            console.log( data )
            self.routes = data
          }

        });
    }
  }
}
</script>

<style>

@import './assets/reset.css';

body {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#app {
  padding-top: 40px;
}

h3 {
  font-weight: bold;
}
.loggedout h3 {
  margin-bottom: 40px;
}

.search {
  align-items: center;
  display: flex;
  justify-content: space-between;
  max-width: 800px;
  margin: 0 auto;
}
.search input {
  -webkit-appearance: none;
  appearance: none;
  border-color: #F3F3F3;
  border-style: solid;
  padding: 10px;
}

table {
  border: 1px solid #F3F3F3;
  border-spacing: 0;
  margin: 20px auto;
  max-width: 800px;
  width: 800px;
}
tr {
  border: none;
}
th,
td {
  border: none;
  padding: 10px;
}
th {
  border-bottom: 1px solid #F3F3F3;
  cursor: pointer;
  font-weight: bold;
}
.name {
  text-align: left;
}
tr:nth-child(even) {
  background: #F9F9F9;
}
</style>
