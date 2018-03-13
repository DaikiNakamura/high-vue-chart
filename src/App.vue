<template>
  <div id="app">
    <div class="form">
      <select v-model="type">
        <option>line</option>
        <option>column</option>
      </select>
      <button v-on:click="addRundom">addRundom</button>
      <button v-on:click="addRundomAuto">addRundomAuto</button>
      <button v-on:click="addRundomStop">addRundomStop</button>
      <button v-on:click="addSeries">addSeries</button>
      <button v-on:click="crearSeries">crearSeries</button>
    </div>
    <HighVueChart class="chart"
                  v-bind:type="type"
                  v-bind:title="chartTitle"
                  v-bind:series="series"
                  v-bind:other-options="chartOptions"></HighVueChart>
    <div class="resultTable">
      <table>
        <tr v-for="lane in series">
          <td>{{ lane.name }}</td>
          <td>
          <span v-for="(val, index) in lane.data">
            <select v-model="lane.data[index]">
              <option v-for="n in 101">{{ n - 1 }}</option>
            </select>
            <button v-on:click="deletePoint(lane.name, index)">x</button>
            ||
          </span>
          </td>
        </tr>
      </table>
    </div>
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
      no: 0,
      chartOptions: {
        tooltip: {
          shared: true,
          crosshairs: true
        }
      }
    }
  },
  methods: {
    addRundom: function() {
      if(this.series.length === 0) {
        alert('no series...');
        return;
      }
      let index = Math.floor( Math.random() * this.series.length);
      let point = Math.floor( Math.random() * 101);
      this.series[index].data.push(point);
    },
    addSeries: function() {
      this.no++;
      this.series.push({name: 'Add' + this.no, data:[0]});
    },
    addRundomAuto: function() {
      let vm = this;
      this.timer = setInterval(function() {
        vm.addRundom();
      }, 100);
    },
    addRundomStop: function() {
      clearInterval(this.timer)
    },
    crearSeries: function() {
      this.series = [{name: 'first', data:[0]}];
    },
    deletePoint: function(name, index) {
      let result = this.series.filter(function(series) {
        return series.name === name;
      });

      result[0].data.splice(index, 1);
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

.form button {
  width: 150px;
  border: 2px solid cornflowerblue;
  background-color: cornflowerblue;
  color: #FFF;
  font-weight: bold;
}
</style>
