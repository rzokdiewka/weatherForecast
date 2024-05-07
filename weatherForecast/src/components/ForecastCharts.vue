<script setup lang="ts">
import * as echarts from 'echarts'
import { onMounted, watch } from 'vue'
import type { ForecastData } from '@/views/MainView.vue'

type EChartsOption = echarts.EChartsOption

let myChart
let option: EChartsOption
const props = defineProps<{
  forecastData: ForecastData
}>()

watch(
  () => props.forecastData.temperature,
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
        data: props.forecastData.date?.map(function (str: string) {
          return str.replace(' ', '\n')
        }),
        axisLabel: {
          interval: 11
        }
      }
    ],
    yAxis: [
      {
        type: 'value',
        name: 'Temperature',
        min:
          Math.min(...props.forecastData.temperature) -
          (Math.min(...props.forecastData.temperature) % 10),
        max:
          Math.max(...props.forecastData.temperature) +
          (Math.max(...props.forecastData.temperature) % 10),
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
          formatter: '{value} mm'
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
        zoomLock: false,
        start: 0,
        end: 100,
        minValueSpan: 24,
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
        data: props.forecastData.temperature
      },
      {
        name: 'Precipitation',
        type: 'bar',
        yAxisIndex: 1,
        data: props.forecastData.precipitation
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
        data: props.forecastData.cloudCover
      }
    ]
  }
  myChart.setOption(option)
}

onMounted(() => {
  drawChart()
})
</script>
