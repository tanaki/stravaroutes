<template>

  <a v-if="(!athlete && !code)" :href="connectionPage">
    <img src="./assets/btn_strava_connectwith_orange.svg" />
  </a>

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
      routes: null
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
        self.fetchAthlete()
      }
    });
  },

  methods: {
    
    fetchAthlete () {

      var self = this;

      const requestOptions = {
        method: "GET",
        headers: { 
          "Content-Type": "application/json",
          "Authorization": "Bearer " + this.code
        }
      };

      fetch( "https://www.strava.com/api/v3/athlete", requestOptions)
        .then(response => response.json())
        .then(data => {

          self.athlete = data
          self.fetchRoutes();

        });

    },

    fetchRoutes () {

      var self = this;

      const requestOptions = {
        method: "GET",
        headers: { 
          "Content-Type": "application/json",
          "Authorization": "Bearer " + this.code
        }
      };

      fetch( "https://www.strava.com/api/v3/athletes/" + this.athlete.id + "/routes?per_page=200", requestOptions)
        .then(response => response.json())
        .then(data => {

          console.log( data )
          self.routes = data

        });
    }
  }
}
</script>

<style>

</style>
