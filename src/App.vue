<template>
  <div id="app">
    <Chart v-if="loadData" :statistics=loadData></Chart>
  </div>
</template>

<script>
import Chart from './components/Chart.vue'
import * as d3 from 'd3'

export default {
  name: 'app',
  components: {
    Chart
  },
  data() {
    return {
      loadData: null
    }
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    async fetchData() {
      const data = await d3.json("./data/info.json");
      this.loadData = data;
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
