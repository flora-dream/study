<template>
  <div id="container"></div>
</template>
<script>
import * as echarts from "echarts"

export default {
  data() {
    return {
      data: []
    }
  },
  mounted() {
    console.log('jgfyjhgv')
    this.initData()
    this.initEcharts()
  },
  methods: {
    initData() {
      for (let i = 0; i < 5; ++i) {
        this.data.push(Math.round(Math.random() * 200));
      }
    },
    updateData() {
      for (let i = 0; i < this.data.length; ++i) {
        if (Math.random() > 0.9) {
          this.data[i] += Math.round(Math.random() * 2000);
        } else {
          this.data[i] += Math.round(Math.random() * 200);
        }
      }
    },
    initEcharts() {
      let option = {
        xAxis: {
          max: 'dataMax'  //用数据的最大值作为 X 轴最大值，视觉效果更好
        },
        yAxis: {
          type: 'category',
          data: ['A', 'B', 'C', 'D', 'E'],
          inverse: true,  //Y 轴从下往上是从小到大的排列
          animationDuration: 300,  // 第一次柱条排序动画的时长
          animationDurationUpdate: 300,  //第一次后柱条排序动画的时长
          max: 2  //想只显示前2名
        },
        series: [
          {
            realtimeSort: true,  // 启动动态排序
            name: 'X',
            type: 'bar',
            data: this.data,
            label: {
              show: true,
              position: 'right',
              valueAnimation: true
            }
          }
        ],
        legend: {
          show: true
        },
        animationDuration: 3000,
        animationDurationUpdate: 3000,
        animationEasing: 'linear',
        animationEasingUpdate: 'linear'
      };
      let myChart = echarts.init(document.getElementById('container'))
      myChart.setOption(option);

      let that = this     // 这个地方要注意！！！ 进入setInterval后this指向window对象，需要提前保存这个this
      setInterval(() => {    // 或者采用箭头函数，因为箭头函数没有this,会继承上一级的this
        // this.updateData();
        for (let i = 0; i < this.data.length; ++i) {
          if (Math.random() > 0.9) {
            this.data[i] += Math.round(Math.random() * 2000);
          } else {
            this.data[i] += Math.round(Math.random() * 200);
          }
        }
        myChart.setOption(option);
      }, 3000);
    },

  }
}
</script>
<style>
#container{
  width: 500px;
  height: 400px;
  background-color: antiquewhite;
  margin: 0 auto;
}
</style>