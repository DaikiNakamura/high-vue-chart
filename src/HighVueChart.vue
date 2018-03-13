<template>
  <div class="highvuechart">
  	<div id="container"></div>
  </div>
</template>

<script>
import Highcharts from 'Highcharts'
export default {
  name: 'HighVueChart',
  props:{
  	title: {
  	  type: String,
  	  required: true
  	},
    series: {
  	  type: Array,
      required: true
    },
    type: {
      type: String,
      required: true
    }
  },
  watch: {
    // hook: change Type
    type: function(after, before) {
      this.changeType(after);
    },

  	// hook: change Title
  	title: function(newTitle, oldTitle) {
  		this.changeTitle(newTitle);
  	},

  	// hook change Series
    series: {
      handler: function(after) {
        var before = this.beforeSeries;
        var vm = this;
        after.forEach(function(afterSeries) {
          var sameBeforeSeries = before.filter(function(beforeSeries) {
            return beforeSeries.name === afterSeries.name;
          });

          // addNewSeries
          if(sameBeforeSeries.length === 0) {
            vm.addSeries(JSON.parse(JSON.stringify(afterSeries)));
            return;
          }

          if(sameBeforeSeries[0].data.length === afterSeries.data.length) {
            // change val
            for(var i = 0; i < afterSeries.data.length; i++) {
              if(sameBeforeSeries[0].data[i] !== afterSeries.data[i]) {
                vm.updateVal(i, afterSeries.name, afterSeries.data[i]);
              }
            }
          }

          if(sameBeforeSeries[0].data.length !== afterSeries.data.length) {
            // addNewPoint
            var point = afterSeries.data[afterSeries.data.length - 1];
            vm.addPoint(afterSeries.name, point);
            return;
          }
        });
        this.beforeSeries = JSON.parse(JSON.stringify(after));
      },
      deep: true
    }
  },
  data: function() {
  	return {
  	  chart: null,
      beforeSeries: null
  	};
  },
  mounted: function() {

    var nowSeries = this.beforeSeries = JSON.parse(JSON.stringify(this.series));
    var options = {
      chart: {
        type: this.type
      },
      title: {
        text: this.title
      },
      series: nowSeries
    };

    var chart = this.createChart(options);
  },
  methods: {

    // create Highcharts object
    createChart: function(options) {

  	  var chart = this.getChart();
  	  if(chart) {
  	    return chart;
      }

      this.chart = Highcharts.chart('container', options);
      return this.chart;
    },

    getChart: function() {
  	  return this.chart;
    },

    // get a Series from highcharts object.
    getSeries: function(name) {

  	  var chart = this.getChart();
  	  if(!chart) {
  	    return null;
      }

      var result = chart.series.filter(function(series) {
        return series.name === name;
      });

      if(result.length === 0) {
        return null;
      }

      return result[0];
    },

    // --- call Highcharts method

    changeTitle: function(title) {

      var chart = this.getChart();
      if(!chart) {
        return;
      }

      chart.setTitle({ text: title });
    },

    changeType: function(type) {

      var chart = this.getChart();
      if(!chart) {
        return;
      }

      chart.update({
        chart: {
          type: type
        }
      });
    },
    addSeries: function(series) {

      var chart = this.getChart();
      if(!chart) {
        return;
      }

      chart.addSeries(series);
    },
    addPoint: function(name, point) {
      var series = this.getSeries(name);
      if(!series) {
        return;
      }

      series.addPoint(point);
    },
    updateVal: function(index, name, point) {
      var series = this.getSeries(name);
      if(!series) {
        return;
      }

      series.data[index].update(parseInt(point, 10));
    }
  }
}
</script>

<style>
</style>
