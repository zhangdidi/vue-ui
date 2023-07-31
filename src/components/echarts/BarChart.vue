<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import * as echarts from 'echarts'
import resize from './mixins/resize'

const animationDuration = 6000

export default {
   mixins: [resize],
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '300px'
    }
  },
  data() {
    return {
      chart: null
    }
  },
  mounted() {
    // let that=this;
    this.$nextTick(() => {
      this.initChart()
    //     window.onresize = function () {
    //     that.chart.resize();
    // }
    })
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    this.chart.dispose()
    this.chart = null
  },
  methods: {
    initChart() {
      this.chart = echarts.init(this.$el, 'macarons')

      this.chart.setOption({
         color: ['#3aa0ff'],
         title:{ 
            text: '分中心前10日交通流量趋势',
            textStyle:{ //标题样式
                color: '#333',
                fontSize:"24",
                
            },
            // borderColor: '#ee3' ,//边框颜色
            // borderRadius: 5, //边框圆角
            // borderWidth: 5 ,//边框宽度
             left:"center",
              top:0
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: { // 坐标轴指示器，坐标轴触发有效
            type: 'shadow' // 默认为直线，可选为：'line' | 'shadow'
          }
        },
        grid: {
          top: 60,
          left: '2%',
          right: '2%',
          bottom: '2%',
          containLabel: true
        },
        xAxis: [{
          type: 'category',
          data: ['1-1', '1-2', '1-3', '1-4', '1-5', '1-6', '1-7','1-8','1-9','1-10'],
          axisTick: {
            alignWithLabel: true
          }
        }],
        yAxis: [{
          type: 'value',
          axisTick: {
            show: false
          }
        }],
        series: [{
          name: 'tokyo',
          type: 'bar',
          stack: 'vistors',
          barWidth: '60%',
          data: [8000, 8500, 8600, 8200, 8100, 8600, 8000,8700,8100,8500],
          // animationDuration
        },
        ]
      })
    }
  }
}
</script>
