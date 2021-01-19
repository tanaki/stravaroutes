<template>
  <table>
    <thead>
      <tr>
        <th @click="sort('name')">Name</th>
        <th @click="sort('distance')">Distance</th>
        <th @click="sort('elevation_gain')">D+</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="route in routes" :key="route.id">
        <td>{{route.name}}</td>
        <td>{{(route.distance / 1000).toFixed(2)}}km</td>
        <td>{{Math.ceil(route.elevation_gain)}}m</td>
        <td>{{route.type}}</td>
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
        currentSortDir:'asc'
      }
    },
    computed: {
      
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

<style>

</style>
