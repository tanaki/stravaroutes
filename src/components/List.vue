<template>

  <div class="search">
    <h3>Strava Routes</h3>
    <input id="search" type="search" placeholder="Search" v-model="searchValue">
  </div>

  <table>
    <thead>
      <tr>
        <th :class="{'selected' : currentSort == 'name' }" @click="sort('name')">Name</th>
        <th :class="{'selected' : currentSort == 'distance' }" @click="sort('distance')">Distance</th>
        <th :class="{'selected' : currentSort == 'elevation_gain' }" @click="sort('elevation_gain')">D+</th>
        <th :class="{'selected' : currentSort == 'type' }" @click="sort('type')">Type</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="route in sortedRoutes" :key="route.id">
        <td class="name">
          <a :href="'https://www.strava.com/routes/'+route.id_str" target="_blank">{{route.name}}</a>
        </td>
        <td class="distance">{{(route.distance / 1000).toFixed(2)}}km</td>
        <td class="elevation">{{Math.ceil(route.elevation_gain)}}m</td>
        <td class="type">{{type[route.type]}}</td>
      </tr>
    </tbody>
  </table>
</template>

<script>
  export default {
    props: {
      routes: Array
    },
    data() {
      return {
        currentSort:'name',
        currentSortDir:'asc',
        searchValue: "",
        type: ['', 'bike', 'run', 'other']
      }
    },

    computed:{
      sortedRoutes() {

        var self = this;

        if (self && self.routes && self.routes.length > 0)

          return this.routes.slice().sort((a,b) => {
            let modifier = 1;
            if(this.currentSortDir === 'desc') modifier = -1;
            if(a[this.currentSort] < b[this.currentSort]) return -1 * modifier;
            if(a[this.currentSort] > b[this.currentSort]) return 1 * modifier;
            return 0;
          }).filter( ( route ) => {
            return ( self.slugify(route.name).toLowerCase().includes( self.slugify(self.searchValue) ) );
          } );

        else
          return []
      }
    },

    mounted() {
      
    },
    methods: {
      
      sort (s) {

        //if s == current sort, reverse
        if(s === this.currentSort) {
          this.currentSortDir = this.currentSortDir==='asc'?'desc':'asc';
        }
        this.currentSort = s;
      },

      slugify (str) {
        var map = {
          'a' : 'á|à|ã|â|À|Á|Ã|Â',
          'e' : 'é|è|ê|É|È|Ê',
          'i' : 'í|ì|î|Í|Ì|Î',
          'o' : 'ó|ò|ô|õ|Ó|Ò|Ô|Õ',
          'u' : 'ú|ù|û|ü|Ú|Ù|Û|Ü',
          'c' : 'ç|Ç',
          'n' : 'ñ|Ñ'
        };
        
        for (var pattern in map) {
          str = str.replace(new RegExp(map[pattern], 'g'), pattern);
        }

        return str;
      }
    }
  }
</script>
