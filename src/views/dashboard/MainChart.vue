<script setup>
import { onMounted, ref, watch } from 'vue'
import { CChart } from '@coreui/vue-chartjs'
import { getStyle } from '@coreui/utils'

const props = defineProps({
  traffic: {
    type: Array,
    default: () => []
  }
})

const mainChartRef = ref()
const data = ref({
  labels: [],
  datasets: [],
})

const updateChartData = () => {
  data.value = {
    labels: props.traffic.map(t => t.recordDate),
    datasets: [
      {
        label: 'Visits',
        backgroundColor: `rgba(${getStyle('--cui-info-rgb')}, .1)`,
        borderColor: getStyle('--cui-info'),
        pointHoverBackgroundColor: getStyle('--cui-info'),
        borderWidth: 2,
        data: props.traffic.map(t => t.visits),
        fill: true,
      },
      {
        label: 'Pageviews',
        backgroundColor: 'transparent',
        borderColor: getStyle('--cui-success'),
        pointHoverBackgroundColor: getStyle('--cui-success'),
        borderWidth: 2,
        data: props.traffic.map(t => t.pageviews),
      },
      {
        label: 'Unique Users',
        backgroundColor: 'transparent',
        borderColor: getStyle('--cui-danger'),
        pointHoverBackgroundColor: getStyle('--cui-danger'),
        borderWidth: 1,
        borderDash: [8, 5],
        data: props.traffic.map(t => t.uniqueUsers),
      },
    ],
  }
}

watch(() => props.traffic, () => {
  updateChartData()
}, { immediate: true })

const options = {
  maintainAspectRatio: false,
  plugins: {
    legend: {
      display: false,
    },
  },
  scales: {
    x: {
      grid: {
        color: getStyle('--cui-border-color-translucent'),
        drawOnChartArea: false,
      },
      ticks: {
        color: getStyle('--cui-body-color'),
      },
    },
    y: {
      beginAtZero: true,
      border: {
        color: getStyle('--cui-border-color-translucent'),
      },
      grid: {
        color: getStyle('--cui-border-color-translucent'),
      },
      ticks: {
        color: getStyle('--cui-body-color'),
        maxTicksLimit: 5,
      },
    },
  },
  elements: {
    line: {
      tension: 0.4,
    },
    point: {
      radius: 0,
      hitRadius: 10,
      hoverRadius: 4,
      hoverBorderWidth: 3,
    },
  },
}

onMounted(() => {
  document.documentElement.addEventListener('ColorSchemeChange', () => {
    if (mainChartRef.value) {
      mainChartRef.value.chart.options.scales.x.grid.borderColor = getStyle(
        '--cui-border-color-translucent',
      )
      mainChartRef.value.chart.options.scales.x.grid.color = getStyle(
        '--cui-border-color-translucent',
      )
      mainChartRef.value.chart.options.scales.x.ticks.color = getStyle('--cui-body-color')
      mainChartRef.value.chart.options.scales.y.grid.borderColor = getStyle(
        '--cui-border-color-translucent',
      )
      mainChartRef.value.chart.options.scales.y.grid.color = getStyle(
        '--cui-border-color-translucent',
      )
      mainChartRef.value.chart.options.scales.y.ticks.color = getStyle('--cui-body-color')
      mainChartRef.value.chart.update()
    }
  })
})
</script>

<template>
  <CChart type="line" :data="data" :options="options" ref="mainChartRef" />
</template>
