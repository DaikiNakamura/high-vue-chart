<template>
  <div id="app">
    <table>
      <tr v-for="lane in series">
        <td>{{ lane.name }}</td>
        <td>
          <span v-for="(val, index) in lane.data">
            <select v-model="lane.data[index]">
              <option v-for="n in 101">{{ n - 1 }}</option>
            </select>
          </span>
        </td>
      </tr>
    </table>
    <select v-model="type">
      <option>line</option>
      <option>column</option>
    </select>
    <button v-on:click="addRundom">addRundom</button>
    <button v-on:click="addRundomAuto">addRundomAuto</button>
    <button v-on:click="addRundomStop">addRundomStop</button>
    <button v-on:click="addSeries">addSeries</button>
    <HighVueChart class="chart" v-bind:type="type" v-bind:title="chartTitle" v-bind:series="series"></HighVueChart>
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
      type: 'line',
      chartTitle: 'Sample',
      series: [{name: 'first', data:[0]}],
      timer: null,
      no: 0
    }
  },
  methods: {
    addRundom: function() {
      if(this.series.length === 0) {
        alert('no series...');
        return;
      }
      var index = Math.floor( Math.random() * this.series.length);
      var point = Math.floor( Math.random() * 101);
      this.series[index].data.push(point);
    },
    addSeries: function() {
      this.no++;
      this.series.push({name: 'Add' + this.no, data:[0]});
    },
    addRundomAuto: function() {
      var vm = this;
      this.timer = setInterval(function() {
        vm.addRundom();
      }, 100);
    },
    addRundomStop: function() {
      clearInterval(this.timer)
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
</style>
