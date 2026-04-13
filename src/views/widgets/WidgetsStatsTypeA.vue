<script setup>
import { onMounted, ref, defineProps, computed } from 'vue'
import { CChart } from '@coreui/vue-chartjs'
import { getStyle } from '@coreui/utils'

const props = defineProps({
  stats: {
    type: Array,
    default: () => []
  },
  traffic: {
    type: Array,
    default: () => []
  }
})

const getStatValue = (category) => {
  const stat = props.stats.find(s => s.category === category)
  return stat ? stat.statValue.toLocaleString() : '0'
}

const getStatPercent = (category) => {
  const stat = props.stats.find(s => s.category === category)
  return stat ? stat.statPercent : 0
}

// Chart Data computed from traffic prop
const chartLabels = computed(() => props.traffic.map(t => t.recordDate))
const visitsTrend = computed(() => props.traffic.map(t => t.visits))
const uniqueTrend = computed(() => props.traffic.map(t => t.uniqueUsers))
const pageviewsTrend = computed(() => props.traffic.map(t => t.pageviews))

const widgetChartRef1 = ref()
const widgetChartRef2 = ref()

onMounted(() => {
  document.documentElement.addEventListener('ColorSchemeChange', () => {
    if (widgetChartRef1.value) {
      widgetChartRef1.value.chart.data.datasets[0].pointBackgroundColor = getStyle('--cui-primary')
      widgetChartRef1.value.chart.update()
    }

    if (widgetChartRef2.value) {
      widgetChartRef2.value.chart.data.datasets[0].pointBackgroundColor = getStyle('--cui-info')
      widgetChartRef2.value.chart.update()
    }
  })
})
</script>

<template>
  <CRow :xs="{ gutter: 4 }">
    <CCol :sm="6" :xl="4" :xxl="3">
      <CWidgetStatsA color="primary">
        <template #value
          >{{ getStatValue('Visits') }}
          <span class="fs-6 fw-normal"> ({{ getStatPercent('Visits') }}% <CIcon icon="cil-arrow-top" />) </span>
        </template>
        <template #title>Total Visits</template>
        <template #chart>
          <CChart
            type="line"
            class="mt-3 mx-3"
            style="height: 70px"
            ref="widgetChartRef1"
            :data="{
              labels: chartLabels,
              datasets: [
                {
                  label: 'Visits',
                  backgroundColor: 'transparent',
                  borderColor: 'rgba(255,255,255,.55)',
                  pointBackgroundColor: getStyle('--cui-primary'),
                  data: visitsTrend,
                },
              ],
            }"
            :options="{
              plugins: { legend: { display: false } },
              maintainAspectRatio: false,
              scales: {
                x: { display: false },
                y: { display: false },
              },
              elements: {
                line: { borderWidth: 1, tension: 0.4 },
                point: { radius: 4, hitRadius: 10, hoverRadius: 4 },
              },
            }"
          />
        </template>
      </CWidgetStatsA>
    </CCol>
    <CCol :sm="6" :xl="4" :xxl="3">
      <CWidgetStatsA color="info">
        <template #value
          >{{ getStatValue('Unique') }}
          <span class="fs-6 fw-normal"> ({{ getStatPercent('Unique') }}% <CIcon icon="cil-arrow-top" />) </span>
        </template>
        <template #title>Unique Users</template>
        <template #chart>
          <CChart
            type="line"
            class="mt-3 mx-3"
            style="height: 70px"
            ref="widgetChartRef2"
            :data="{
              labels: chartLabels,
              datasets: [
                {
                  label: 'Unique',
                  backgroundColor: 'transparent',
                  borderColor: 'rgba(255,255,255,.55)',
                  pointBackgroundColor: getStyle('--cui-info'),
                  data: uniqueTrend,
                },
              ],
            }"
            :options="{
              plugins: { legend: { display: false } },
              maintainAspectRatio: false,
              scales: {
                x: { display: false },
                y: { display: false },
              },
              elements: {
                line: { borderWidth: 1 },
                point: { radius: 4, hitRadius: 10, hoverRadius: 4 },
              },
            }"
          />
        </template>
      </CWidgetStatsA>
    </CCol>
    <CCol :sm="6" :xl="4" :xxl="3">
      <CWidgetStatsA color="warning">
        <template #value
          >{{ getStatPercent('Bounce Rate') }}%
          <span class="fs-6 fw-normal"> (AVG <CIcon icon="cil-arrow-right" />) </span>
        </template>
        <template #title>Bounce Rate</template>
        <template #chart>
          <CChart
            type="line"
            class="mt-3"
            style="height: 70px"
            :data="{
              labels: chartLabels,
              datasets: [
                {
                  label: 'Trend',
                  backgroundColor: 'rgba(255,255,255,.2)',
                  borderColor: 'rgba(255,255,255,.55)',
                  data: visitsTrend,
                  fill: true,
                },
              ],
            }"
            :options="{
              plugins: { legend: { display: false } },
              maintainAspectRatio: false,
              scales: {
                x: { display: false },
                y: { display: false },
              },
              elements: {
                line: { borderWidth: 2, tension: 0.4 },
                point: { radius: 0 },
              },
            }"
          />
        </template>
      </CWidgetStatsA>
    </CCol>
    <CCol :sm="6" :xl="4" :xxl="3">
      <CWidgetStatsA color="danger">
        <template #value
          >{{ getStatValue('Pageviews') }}
          <span class="fs-6 fw-normal"> ({{ getStatPercent('Pageviews') }}% <CIcon icon="cil-arrow-top" />) </span>
        </template>
        <template #title>Total Pageviews</template>
        <template #chart>
          <CChart
            type="bar"
            class="mt-3 mx-3"
            style="height: 70px"
            :data="{
              labels: chartLabels,
              datasets: [
                {
                  label: 'Pageviews',
                  backgroundColor: 'rgba(255,255,255,.2)',
                  borderColor: 'rgba(255,255,255,.55)',
                  data: pageviewsTrend,
                  barPercentage: 0.6,
                },
              ],
            }"
            :options="{
              maintainAspectRatio: false,
              plugins: { legend: { display: false } },
              scales: {
                x: { display: false },
                y: { display: false },
              },
            }"
          />
        </template>
      </CWidgetStatsA>
    </CCol>
  </CRow>
</template>
