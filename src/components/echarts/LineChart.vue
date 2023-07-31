<template>
  <div :class="className" :style="{ height: height, width: width }" />
</template>

<script>
import * as echarts from "echarts";
import resize from "./mixins/resize";

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: "chart",
    },
    width: {
      type: String,
      default: "100%",
    },
    height: {
      type: String,
      default: "400px",
    },
    autoResize: {
      type: Boolean,
      default: true,
    },
    chartData: {
      type: Object,
      required: true,
    },
    xData:{
      type:Array,
      default:["1-1", "1-2", "1-3","1-4", "1-5", "1-6", "1-7", "1-8","1-9", "1-10"]
    },
    legendData:{
      type:Array,
      default:["london"]
    },

    echartTitle:{
      type:String,
      default:"门架前10日交通流量趋势"
    }
  },
  data() {
    return {
      chart: null,
      series:null,
    };
  },
  watch: {
    chartData: {
      deep: true,
      handler(val) {
        this.setOptions(val);
      },
    },
  },
  mounted() {
    this.$nextTick(() => {
      this.initChart();
    });
  },
  beforeDestroy() {
    if (!this.chart) {
      return;
    }
    this.chart.dispose();
    this.chart = null;
  },
  methods: {
    initChart() {
      this.chart = echarts.init(this.$el, "macarons");
      this.setOptions(this.chartData);
    },
    setOptions({ expectedData, actualData } = {}) {
      let seriesArr=[];
      let color=[
        '#1890ff',
        '#2fc25b',
        '#fbd643',
        '#00BC42',
        '#FF4D4F'
      ]
      this.legendData.forEach((ele,i) => {
         seriesArr.push({
          name: ele,
            itemStyle: {
              normal: {
                color: color[i],
                lineStyle: {
                  color: color[i],
                  width: 2,
                },
              },
            },
            smooth: true,
            type: "line",
            data: expectedData[i],
            animationDuration: 2800,
            animationEasing: "cubicInOut",
         })
      });
      this.chart.setOption({
        xAxis: {
          data: this.xData,
          boundaryGap: false,
          axisTick: {
            show: false,
          },
        },
         color: ['#3aa0ff'],
        title: {
          text: this.echartTitle,
          textStyle: {
            //标题样式
            color: "#333",
            fontSize: "24",
          },
          // borderColor: '#ee3' ,//边框颜色
          // borderRadius: 5, //边框圆角
          // borderWidth: 5 ,//边框宽度
          left: "center",
          top: 0,
        },
        grid: {
          left: "2%",
          right: "2%",
          bottom: "15%",
          top: 60,
          containLabel: true,
        },
        tooltip: {
          trigger: "axis",
          // axisPointer: {
          //   type: "cross",
          // },
          padding: [5, 10],
        },
        yAxis: {
          axisTick: {
            show: false,
          },
        },
        legend: {
          data:this.legendData,
          // type:"scroll",
          bottom:20,
          itemWidth:16,
          itemHeight:4,
          // icon:"circle",
          fontSize:"14",
          padding:[5,0],
        },
        series: seriesArr,
      });
    },
  },
};
</script>
