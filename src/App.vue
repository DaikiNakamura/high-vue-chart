<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>{{ msg }}</h1>
    <HighVueChart class="chart" v-bind:title="chartTitle"></HighVueChart>
    <table>
      <tr v-for="lane in series">
        <td>{{ lane.name }}</td>
        <td>
          <span v-for="point in lane.points">{{ point }}|</span>
        </td>
      </tr>
    </table>
    <button v-on:click="addRundom">addRundom</button>
  </div>
</template>

<script>
import HighVueChart from './HighVueChart'

export default {
  name: 'app',
  components: {
    'HighVueChart': HighVueChart
  },
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      chartTitle: 'Sample',
      series: [{
        name: 'abc',
        points: [10, 20, 30]
      }, {
        name: 'def',
        points: [20, 10, 40]
      }]
    }
  },
  methods: {
    addRundom: function() {
      let index = Math.floor( Math.random() * 2 );
      let point = Math.floor( Math.random() * 101);
      let target = this.series[index];
      target.points.push(point);

      // title change
      this.chartTitle = 'addRundom';
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

h1, h2 {
  font-weight: normal;
}

table, tr, td {
  border: 1px solid #000;
}

.chart {
  width: 500px;
  height: 400px;
}
</style>
