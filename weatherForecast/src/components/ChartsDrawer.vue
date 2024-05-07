<script setup lang="ts">
import * as echarts from 'echarts'
import { onMounted, watch } from 'vue'

type EChartsOption = echarts.EChartsOption

let myChart
let option: EChartsOption
const props = defineProps({
  date: [] as string[],
  temperature: [] as number[],
  pressure: [] as number[],
  precipitation: [] as string[],
  cloudCover: [] as number[],
  wind: [] as number[]
})

watch(
  () => props.temperature,
  () => {
    drawChart()
  }
)

function drawChart() {
  const chartDom = document.getElementById('chart-container')

  myChart = echarts.init(chartDom)

  const colors = ['#c65454', '#668dee', '#a8a8a8']

  option = {
    color: colors,
    tooltip: {
      trigger: 'axis',
      axisPointer: {
        type: 'cross'
      },
      textStyle: {
        fontSize: 12
      }
    },
    grid: {
      right: '15%',
      bottom: '15%'
    },
    toolbox: {
      feature: {
        // dataView: { show: true, readOnly: false },
        restore: { show: true },
        saveAsImage: { show: true }
      }
    },
    legend: {
      data: ['Temperature', 'Pressure', 'Precipitation', 'Cloud cover', 'Wind speed']
    },
    xAxis: [
      {
        type: 'category',
        axisTick: {
          alignWithLabel: true
        },
        data: props.date.map(function (str: string) {
          return str.replace(' ', '\n')
        }),
        axisLabel: {
          interval: 23
        }
      }
    ],
    yAxis: [
      {
        type: 'value',
        name: 'Temperature',
        min: Math.min(...props.temperature) - (Math.min(...props.temperature) % 10),
        max: Math.max(...props.temperature) + (Math.max(...props.temperature) % 10),
        position: 'left',
        alignTicks: true,
        axisLine: {
          show: true,
          lineStyle: {
            color: colors[0]
          }
        },
        axisLabel: {
          formatter: '{value} °C'
        }
      },
      {
        type: 'value',
        name: 'Precipitation',
        min: 0,
        max: 20,
        interval: 2,
        position: 'right',
        alignTicks: true,
        axisLine: {
          show: true,
          lineStyle: {
            color: colors[1]
          }
        },
        axisLabel: {
          formatter: '{value} ml'
        }
      },
      {
        type: 'value',
        name: 'Cloud cover',
        position: 'right',
        alignTicks: true,
        offset: 70,
        min: 0,
        max: 100,
        axisLine: {
          show: true,
          lineStyle: {
            color: colors[2]
          }
        },
        axisLabel: {
          formatter: '{value} %'
        }
      }
    ],
    dataZoom: [
      {
        type: 'slider',
        xAxisIndex: 0,
        zoomLock: true,
        start: 0,
        end: 100,
        handleSize: 20,
        right: 'ph',
        top: 'ph',
        width: 'ph',
        height: 'ph'
      }
    ],
    series: [
      {
        name: 'Temperature',
        type: 'line',
        smooth: true,
        data: props.temperature
      },
      // {
      //   name: 'Pressure',
      //   type: 'bar',
      //   yAxisIndex: 1,
      //   data: props.pressure
      // },
      {
        name: 'Precipitation',
        type: 'bar',
        yAxisIndex: 1,
        data: props.precipitation
      },
      {
        name: 'Cloud cover',
        type: 'line',
        smooth: true,
        yAxisIndex: 2,
        lineStyle: {
          opacity: 1,
          width: 0
        },
        areaStyle: {
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
            {
              offset: 0,
              color: 'rgb(168,168,168)'
            },
            {
              offset: 1,
              color: 'rgba(168,168,168,0.21)'
            }
          ])
        },
        data: props.cloudCover
      }
      // {
      //   name: 'Wind speed',
      //   type: 'line',
      //   yAxisIndex: 4,
      //   data: props.wind
      // }
    ]
  }
  myChart.setOption(option)
}

onMounted(() => {
  drawChart()
})
</script>

<!--<template>-->
<!--  <div id="chart-container" style="width: 1000px; height: 600px"/>-->
<!--</template>-->

<style scoped></style>
