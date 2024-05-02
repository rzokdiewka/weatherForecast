<script setup lang="ts">
import * as echarts from 'echarts'
import { onMounted, ref, toRaw, watch } from 'vue'
import WeatherData from '@/components/Forecast.vue'
type EChartsOption = echarts.EChartsOption

// console.log(chartDom)
let myChart
let option: EChartsOption
const props = defineProps({
  date: Array<String>,
  temperature: Array<Number>,
  pressure: Array<Number>,
  precipitation: Array<Number>,
  cloud: Array<Number>,
  wind: Array<Number>
})
const data = ref()

watch(
  () => props.temperature,
  () => {
    // data.value = toRaw(props.data)
    console.log('chart', data.value)
    drawChart()
  }
)
console.log('data charts', data.value)

function drawChart() {
  // data.value = toRaw(props.data)

  console.log(
    'draw',
    props.temperature,
    Math.min(...props.pressure) - (Math.min(...props.pressure) % 10),
    Math.max(...props.pressure) + (Math.max(...props.pressure) % 10)
  )
  const chartDom = document.getElementById('chart-container')

  myChart = echarts.init(chartDom)

  const colors = ['#c65454', '#e015ec', '#668dee', '#FFDA44FF', '#91CC75']

  option = {
    color: colors,

    tooltip: {
      trigger: 'axis',
      axisPointer: {
        type: 'cross'
      }
    },
    grid: {
      right: '20%'
    },
    toolbox: {
      feature: {
        dataView: { show: true, readOnly: false },
        restore: { show: true },
        saveAsImage: { show: true }
      }
    },
    legend: {
      data: ['Temperature', 'Pressure', 'Precipitation', 'Cloud cover', 'Wind speed']
    },
    // visualMap: [
    //   {
    //     show: false,
    //     type: 'continuous',
    //     seriesIndex: 3,
    //     min: 0,
    //     max: 100
    //   },
    // ],
    xAxis: [
      {
        type: 'category',
        axisTick: {
          alignWithLabel: true
        },
        // prettier-ignore
        data: props.date
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
        name: 'Pressure',
        min: Math.min(...props.pressure) - (Math.min(...props.pressure) % 10),
        max: Math.max(...props.pressure) + (Math.max(...props.pressure) % 10),
        position: 'right',
        alignTicks: true,
        offset: 70,
        axisLine: {
          show: true,
          lineStyle: {
            color: colors[1]
          }
        },
        axisLabel: {
          formatter: '{value} hPa'
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
        // offset: 80,
        axisLine: {
          show: true,
          lineStyle: {
            color: colors[2]
          }
        },
        axisLabel: {
          formatter: '{value} ml'
        }
      },
      {
        type: 'value',
        name: 'Clear sky',
        position: 'right',
        alignTicks: true,
        offset: 210,
        axisLine: {
          show: true,
          lineStyle: {
            color: colors[3]
          }
        },
        axisLabel: {
          formatter: '{value} %'
        }
      },
      {
        type: 'value',
        name: 'Wind speed',
        position: 'right',
        alignTicks: true,
        offset: 140,
        axisLine: {
          show: true,
          lineStyle: {
            color: colors[4]
          }
        },
        axisLabel: {
          formatter: '{value} km/h'
        }
      }
    ],
    dataZoom: [
      {
        type: 'slider',
        xAxisIndex: 0,
        zoomLock: true,
        width: 800,
        right: 100,
        start: 0,
        end: 100,
        handleSize: 20
      }
    ],
    series: [
      {
        name: 'Temperature',
        type: 'line',
        smooth: true,
        data: props.temperature
      },
      {
        name: 'Pressure',
        type: 'bar',
        yAxisIndex: 1,
        data: props.pressure
      },
      {
        name: 'Precipitation',
        type: 'bar',
        yAxisIndex: 2,
        data: props.precipitation
      },
      {
        name: 'Cloud cover',
        type: 'line',
        smooth: true,
        yAxisIndex: 3,
        lineStyle: {
          opacity: 1,
          width: 0
        },
        areaStyle: {
          color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
            {
              offset: 0,
              color: 'rgb(255,218,68)'
            },
            {
              offset: 1,
              color: 'rgba(255,218,68,0.07)'
            }
          ])
        },
        data: props.cloud
      },
      {
        name: 'Wind speed',
        type: 'line',
        yAxisIndex: 4,
        data: props.wind
      }
    ]
  }
  myChart.setOption(option)
}

onMounted(() => {
  drawChart()
})
</script>

<template></template>

<style scoped></style>
