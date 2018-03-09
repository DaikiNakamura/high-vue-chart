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
  	}
  },
  watch: {

  	// hook: change Title
  	'title': function(newTitle, oldTitle) {
  		this.changeTitle(newTitle);
  	}
  },
  data: function() {
  	return {
  	  chartObj: null
  	};
  },
  mounted: function() {
  	this.chartObj = Highcharts.chart('container', {
        chart: {
            type: 'bar'
        },
        title: {
            text: this.title // from Props
        },
        xAxis: {
            categories: ['Apples', 'Bananas', 'Oranges']
        },
        yAxis: {
            title: {
                text: 'Fruit eaten'
            }
        },
        series: [{
            name: 'Jane',
            data: [1, 0, 4]
        }, {
            name: 'John',
            data: [5, 7, 3]
        }]
    });
  },
  methods: {
  	changeTitle: function(title) {
  		this.chartObj.setTitle({ text: title });
  	}
  }
}
</script>

<style>
</style>