<template>

  <div class="search">
    <h3>Strava Routes</h3>
    <input id="search" type="search" placeholder="Search" v-model="searchValue">
  </div>

  <table>
    <thead>
      <tr>
        <th class="name" @click="sort('name')">Name</th>
        <th @click="sort('distance')">Distance</th>
        <th @click="sort('elevation_gain')">D+</th>
        <th @click="sort('type')">Type</th>
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
            return ( route.name.toLowerCase().includes( self.searchValue ) );
          } );

        else
          return []
      }
    },

    mounted() {
      
    },
    methods: {
      
      sort:function(s) {

        //if s == current sort, reverse
        if(s === this.currentSort) {
          this.currentSortDir = this.currentSortDir==='asc'?'desc':'asc';
        }
        this.currentSort = s;
      }
    }
  }
</script>
