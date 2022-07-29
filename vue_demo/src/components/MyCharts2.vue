<template>
  <div id="container2"></div>
</template>
<script>
import * as echarts from "echarts"

export default {
  data() {
    return {
      data: [900, 345, 393, -108, -154, 135, 178, 286, -119, -361, -203],
      help: [],
      positive: [],
      negative: [],
    }
  },
  mounted() {
    this.initData()
    this.initCharts()
  },
  methods: {
    initData() {
      for (let i = 0, sum = 0; i < this.data.length; ++i) {
        if (this.data[i] >= 0) {
          this.positive.push(this.data[i]);
          this.negative.push('-');
        } else {
          this.positive.push('-');
          this.negative.push(-this.data[i]);
        }

        if (i === 0) {
          this.help.push(0);
        } else {
          sum += this.data[i - 1];
          if (this.data[i] < 0) {
            this.help.push(sum + this.data[i]);
          } else {
            this.help.push(sum);
          }
        }
      }
      console.log(this.help)
    },
    initCharts() {
      let option = {
        title: {
          text: 'Waterfall'
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          containLabel: true
        },
        xAxis: {
          type: 'category',
          splitLine: { show: false },
          data: (() => {
            let list = [];
            for (let i = 1; i <= 11; i++) {
              list.push('Oct/' + i);
            }
            return list;
          })()
        },
        yAxis: {
          type: 'value'
        },
        series: [
          {
            type: 'bar',
            stack: 'all',
            itemStyle: {
              normal: {
                barBorderColor: 'rgba(0,0,0,0)',
                color: 'rgba(0,0,0,0)'
              },
              emphasis: {
                barBorderColor: 'rgba(0,0,0,0)',
                color: 'rgba(0,0,0,0)'
              }
            },
            data: this.help
          },
          {
            name: 'positive',
            type: 'bar',
            stack: 'all',
            data: this.positive
          },
          {
            name: 'negative',
            type: 'bar',
            stack: 'all',
            data: this.negative,
            itemStyle: {
              color: '#f33'
            }
          }
        ]
      }
      let myChart = echarts.init(document.getElementById('container2'))
      myChart.setOption(option)
    }
  }
}
</script>
<style>
#container2{
  width: 500px;
  height: 400px;
  background-color: #42b983;
  margin: 0 auto;
}
</style>