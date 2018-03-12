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
        var before = this.oldSeries;
        var vm = this;
        after.forEach(function(series, index) {
          var sameBeforeSeries = before.filter(function(beforeSeries) {
            return beforeSeries.name === series.name;
          });

          // addNewSeries
          if(sameBeforeSeries.length === 0) {
            vm.addSeries(JSON.parse(JSON.stringify(series)));
            return;
          }

          if(sameBeforeSeries[0].data.length === series.data.length) {
            // change val
            for(var i = 0; i < series.data.length; i++) {
              if(sameBeforeSeries[0].data[i] !== series.data[i]) {
                vm.updateVal(i, series.name, series.data[i]);
              }
            }
          }

          if(sameBeforeSeries[0].data.length !== series.data.length) {
            // addNewPoint
            var point = series.data[series.data.length - 1];
            vm.addPoint(series.name, point);
            return;
          }
        });
        this.oldSeries = JSON.parse(JSON.stringify(after));
      },
      deep: true
    }
  },
  data: function() {
  	return {
  	  chartObj: null,
      nowSeries: null,
      oldSeries: null
  	};
  },
  mounted: function() {

    this.nowSeries = JSON.parse(JSON.stringify(this.series));

  	this.chartObj = Highcharts.chart('container', {
        chart: {
            type: this.type
        },
        title: {
            text: this.title
        },
        series: this.nowSeries
    });

  	// copy
  	this.oldSeries = JSON.parse(JSON.stringify(this.series));
  },
  methods: {
  	changeTitle: function(title) {
  		this.chartObj.setTitle({ text: title });
  	},
    changeType: function(type) {
  	  this.chartObj.update({
        chart: {
          type: type
        }
      })
    },
    addSeries: function(series) {
  	  this.chartObj.addSeries(series);
    },
    addPoint: function(name, point) {
  	  var targetSeries = this.chartObj.series.filter(function(series) {
  	    return series.name === name;
      });

  	  if(targetSeries.length === 0) {
  	    return;
      }

      targetSeries[0].addPoint(point);
    },
    updateVal: function(index, name, point) {
      var targetSeries = this.chartObj.series.filter(function(series) {
        return series.name === name;
      });

      if(targetSeries.length === 0) {
        return;
      }
      targetSeries[0].data[index].update(parseInt(point, 10));
    }
  }
}
</script>

<style>
</style>
