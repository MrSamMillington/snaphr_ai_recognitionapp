<template>
   <div>
    <v-btn icon slot="widget-header-action">
      <v-icon class="text--secondary" @click="fillData()">refresh</v-icon>
    </v-btn>
    <div slot="widget-content">
      <div class="small">
        <bar-chart :chart-data="datacollection"
        :height="height"
        :width="width"
        :options="options"
        :responsive=true
        >
      </bar-chart>
      </div>
    </div>
  </div>
</template>

<script>

  import BarChart from './bar.js'

  export default {
    props: ['height', 'width', 'data'],
    components: {
      BarChart,
    },
    data () {
      return {
        yAxisLabel: 'confidence',
        datacollection: null,
      }
    },
    mounted () {
      this.fillData()
    },
    watch:{
      data: function(){
        this.fillData();
      }
    },
    methods: {
      fillData () {
        let labels = [];
        let aidata = [];
        this.data.forEach(d => {
          labels.push(d.name);
          aidata.push(d.value);
        })

        this.datacollection = {
          labels: labels,
          datasets: [
            {
              label: 'Whats in my picture',
              backgroundColor: '#4286f4',
              data: aidata
            }
          ]
        }
      }
    },
    computed: {
      options(){
        return {
          scales: {
            yAxes: [{
              display: true,
              scaleLabel: {
                display: true,
                labelString: this.yAxisLabel
              }
            }],
            xAxes: [
              {
                ticks: {
                  autoSkip: false
                }
              }
            ]
          },
          legend: {
            display: false
          },
          responsive: true,
          maintainAspectRatio: false
        }
      }
    }
  }
</script>

<style>

</style>
