<template>
  <div class="highvuechart">
  	<div id="container"></div>
  </div>
</template>

<script>
import Highcharts from 'highcharts'
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
    },
    otherOptions: {
  	  type: Object,
      required: false,
      default: {}
    }
  },
  watch: {
    // hook: change Type
    type: function(afterType) {
      this.changeType(afterType);
    },

  	// hook: change Title
  	title: function(afterTitle) {
  		this.changeTitle(afterTitle);
  	},

  	// hook change Series
    series: {
      handler: function(after) {
        let before = this.beforeSeries;
        let vm = this;
        after.forEach(function(afterSeries) {
          let sameBeforeSeries = before.filter(function(beforeSeries) {
            return beforeSeries.name === afterSeries.name;
          });

          // addNewSeries
          if(sameBeforeSeries.length === 0) {
            vm.addSeries(JSON.parse(JSON.stringify(afterSeries)));
            return;
          }

          let beforeSeries = sameBeforeSeries[0];
          let beforeDataLength = beforeSeries.data.length;
          let afterDataLength = afterSeries.data.length;
          if(beforeDataLength === afterDataLength) {
            // change Point value
            for(let i = 0; i < afterDataLength; i++) {
              if(beforeSeries.data[i] !== afterSeries.data[i]) {
                vm.updateVal(i, afterSeries.name, afterSeries.data[i]);
              }
            }
          }

          if(beforeDataLength < afterDataLength) {

            // add Point
            let point = afterSeries.data[afterDataLength - 1];
            vm.addPoint(afterSeries.name, point);
            return;

          } else if(beforeDataLength > afterDataLength) {

            // delete Point
            let cursor = 0;
            for(let i = 0; i < beforeDataLength; i++) {
              if(beforeSeries.data[i] !== afterSeries.data[cursor]) {
                vm.removePoint(afterSeries.name, i);
              } else {
                cursor++;
              }
            }
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

    let nowSeries = this.beforeSeries = JSON.parse(JSON.stringify(this.series));
    let options = {
      chart: {
        type: this.type
      },
      title: {
        text: this.title
      },
      series: nowSeries
    };

    if(this.otherOptions) {
      options = Object.assign(options, this.otherOptions);
    }

    this.createChart(options);
  },
  methods: {

    // create Highcharts object
    createChart: function(options) {

      let chart = this.getChart();
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

      let chart = this.getChart();
  	  if(!chart) {
  	    return null;
      }

      let result = chart.series.filter(function(series) {
        return series.name === name;
      });

      if(result.length === 0) {
        return null;
      }

      return result[0];
    },

    // --- call Highcharts method

    changeTitle: function(title) {

      let chart = this.getChart();
      if(!chart) {
        return;
      }

      chart.setTitle({ text: title });
    },

    changeType: function(type) {

      let chart = this.getChart();
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

      let chart = this.getChart();
      if(!chart) {
        return;
      }

      chart.addSeries(series);
    },
    addPoint: function(name, point) {
      let series = this.getSeries(name);
      if(!series) {
        return;
      }

      series.addPoint(point);
    },
    updateVal: function(index, name, point) {
      let series = this.getSeries(name);
      if(!series) {
        return;
      }

      series.data[index].update(parseInt(point, 10));
    },
    removePoint: function(name, index) {
      let series = this.getSeries(name);
      if(!series) {
        return;
      }

      series.removePoint(index);
    }
  }
}
</script>

<style>
</style>
