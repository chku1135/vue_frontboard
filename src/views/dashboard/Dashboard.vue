<script setup>
import { onMounted, ref } from 'vue'
import avatar1 from '@/assets/images/avatars/1.jpg'
import avatar2 from '@/assets/images/avatars/2.jpg'
import avatar3 from '@/assets/images/avatars/3.jpg'
import avatar4 from '@/assets/images/avatars/4.jpg'
import avatar5 from '@/assets/images/avatars/5.jpg'
import avatar6 from '@/assets/images/avatars/6.jpg'
import MainChart from './MainChart.vue'
import WidgetsStatsA from './../widgets/WidgetsStatsTypeA.vue'
import WidgetsStatsD from './../widgets/WidgetsStatsTypeD.vue'

const trafficData = ref([])
const statsData = ref([])
const tableExample = ref([])

const progressGroupExample1 = ref([])
const progressGroupExample2 = [
  { title: 'Male', icon: 'cil-user', value: 53 },
  { title: 'Female', icon: 'cil-user-female', value: 43 },
]
const progressGroupExample3 = [
  {
    title: 'Organic Search',
    icon: 'cib-google',
    percent: 56,
    value: '191,235',
  },
  { title: 'Facebook', icon: 'cib-facebook', percent: 15, value: '51,223' },
  { title: 'Twitter', icon: 'cib-twitter', percent: 11, value: '37,564' },
  { title: 'LinkedIn', icon: 'cib-linkedin', percent: 8, value: '27,319' },
]

const avatars = [avatar1, avatar2, avatar3, avatar4, avatar5, avatar6]

const fetchDashboardData = async () => {
  try {
    const response = await fetch('/api/dashboard/summary')
    const data = await response.json()
    
    // 1. Traffic Data (Main Chart)
    trafficData.value = data.traffic.reverse() // 날짜 순 정렬
    
    // 2. Stats Data (Widgets & Footer)
    statsData.value = data.stats
    
    // 3. User Activity Table
    tableExample.value = data.users.map((user, index) => ({
      avatar: { src: avatars[index % avatars.length], status: user.isNew ? 'success' : 'secondary' },
      user: {
        name: user.username,
        new: user.isNew,
        registered: user.registeredAt,
      },
      country: { name: 'Local', flag: user.countryCode },
      usage: {
        value: user.usagePercent,
        period: user.period,
        color: user.usagePercent > 70 ? 'danger' : (user.usagePercent > 40 ? 'warning' : 'success'),
      },
      payment: { name: user.paymentMethod, icon: 'cil-credit-card' },
      activity: user.lastActivity,
    }))

    // 4. Progress Group 1 (Daily Traffic)
    progressGroupExample1.value = data.traffic.map(t => ({
      title: t.recordDate,
      value1: (t.visits / 3000) * 100, // 더미 대비 비율 계산
      value2: (t.pageviews / 10000) * 100
    }))

  } catch (error) {
    console.error('Failed to fetch dashboard data:', error)
  }
}

const getStatValue = (category) => {
  const stat = statsData.value.find(s => s.category === category)
  return stat ? `${stat.statValue.toLocaleString()} ${stat.subCategory} (${stat.statPercent}%)` : '0'
}

const getStatPercent = (category) => {
  const stat = statsData.value.find(s => s.category === category)
  return stat ? stat.statPercent : 0
}

onMounted(() => {
  fetchDashboardData()
})
</script>

<template>
  <div>
    <WidgetsStatsA class="mb-4" :stats="statsData" :traffic="trafficData" />
    <CRow>
      <CCol :md="12">
        <CCard class="mb-4">
          <CCardBody>
            <CRow>
              <CCol :sm="5">
                <h4 id="traffic" class="card-title mb-0">Traffic</h4>
                <div class="small text-body-secondary">Real-time Data from DB</div>
              </CCol>
              <CCol :sm="7" class="d-none d-md-block">
                <CButton color="primary" class="float-end">
                  <CIcon icon="cil-cloud-download" />
                </CButton>
              </CCol>
            </CRow>
            <CRow>
              <MainChart 
                v-if="trafficData.length > 0"
                :traffic="trafficData"
                style="height: 300px; max-height: 300px; margin-top: 40px" 
              />
            </CRow>
          </CCardBody>
          <CCardFooter>
            <CRow
              :xs="{ cols: 1, gutter: 4 }"
              :sm="{ cols: 2 }"
              :lg="{ cols: 4 }"
              :xl="{ cols: 5 }"
              class="mb-2 text-center"
            >
              <CCol v-for="cat in ['Visits', 'Unique', 'Pageviews', 'New Users', 'Bounce Rate']" :key="cat">
                <div class="text-body-secondary">{{ cat }}</div>
                <div class="fw-semibold text-truncate">{{ getStatValue(cat) }}</div>
                <CProgress class="mt-2" :color="cat === 'New Users' ? 'danger' : 'info'" thin :precision="1" :value="getStatPercent(cat)" />
              </CCol>
            </CRow>
          </CCardFooter>
        </CCard>
      </CCol>
    </CRow>
    <WidgetsStatsD class="mb-4" />
    <CRow>
      <CCol :md="12">
        <CCard class="mb-4">
          <CCardHeader> Traffic &amp; Sales (Live) </CCardHeader>
          <CCardBody>
            <CRow>
              <CCol :sm="12" :lg="6">
                <CRow>
                  <CCol :xs="6">
                    <div class="border-start border-start-4 border-start-info py-1 px-3 mb-3">
                      <div class="text-body-secondary small">New Clients</div>
                      <div class="fs-5 fw-semibold">{{ statsData.find(s => s.category === 'New Clients')?.statValue || 0 }}</div>
                    </div>
                  </CCol>
                  <CCol :xs="6">
                    <div class="border-start border-start-4 border-start-danger py-1 px-3 mb-3">
                      <div class="text-body-secondary small">Recurring Clients</div>
                      <div class="fs-5 fw-semibold">{{ statsData.find(s => s.category === 'Recurring Clients')?.statValue || 0 }}</div>
                    </div>
                  </CCol>
                </CRow>
                <hr class="mt-0" />
                <div
                  v-for="item in progressGroupExample1"
                  :key="item.title"
                  class="progress-group mb-4"
                >
                  <div class="progress-group-prepend">
                    <span class="text-body-secondary small">{{ item.title }}</span>
                  </div>
                  <div class="progress-group-bars">
                    <CProgress thin color="info" :value="item.value1" />
                    <CProgress thin color="danger" :value="item.value2" />
                  </div>
                </div>
              </CCol>
              <CCol :sm="12" :lg="6">
                <CRow>
                  <CCol :xs="6">
                    <div class="border-start border-start-4 border-start-warning py-1 px-3 mb-3">
                      <div class="text-body-secondary small">Pageviews (Total)</div>
                      <div class="fs-5 fw-semibold">{{ statsData.find(s => s.category === 'Pageviews')?.statValue || 0 }}</div>
                    </div>
                  </CCol>
                  <CCol :xs="6">
                    <div class="border-start border-start-4 border-start-success py-1 px-3 mb-3">
                      <div class="text-body-secondary small">Organic Traffic</div>
                      <div class="fs-5 fw-semibold">49,123</div>
                    </div>
                  </CCol>
                </CRow>
                <hr class="mt-0" />
                <div v-for="item in progressGroupExample2" :key="item.title" class="progress-group">
                  <div class="progress-group-header">
                    <CIcon :icon="item.icon" class="me-2" size="lg" />
                    <span class="title">{{ item.title }}</span>
                    <span class="ms-auto fw-semibold">{{ item.value }}%</span>
                  </div>
                  <div class="progress-group-bars">
                    <CProgress thin :value="item.value" color="warning" />
                  </div>
                </div>

                <div class="mb-5"></div>

                <div v-for="item in progressGroupExample3" :key="item.title" class="progress-group">
                  <div class="progress-group-header">
                    <CIcon :icon="item.icon" class="me-2" size="lg" />
                    <span class="title">{{ item.title }}</span>
                    <span class="ms-auto fw-semibold">
                      {{ item.value }}
                      <span class="text-body-secondary small">({{ item.percent }}%)</span>
                    </span>
                  </div>
                  <div class="progress-group-bars">
                    <CProgress thin :value="item.percent" color="success" />
                  </div>
                </div>
              </CCol>
            </CRow>
            <br />
            <CTable align="middle" class="mb-0 border" hover responsive>
              <CTableHead class="text-nowrap">
                <CTableRow>
                  <CTableHeaderCell class="bg-body-secondary text-center">
                    <CIcon icon="cil-people" />
                  </CTableHeaderCell>
                  <CTableHeaderCell class="bg-body-secondary"> User </CTableHeaderCell>
                  <CTableHeaderCell class="bg-body-secondary text-center">
                    Country
                  </CTableHeaderCell>
                  <CTableHeaderCell class="bg-body-secondary"> Usage </CTableHeaderCell>
                  <CTableHeaderCell class="bg-body-secondary text-center">
                    Payment Method
                  </CTableHeaderCell>
                  <CTableHeaderCell class="bg-body-secondary"> Activity </CTableHeaderCell>
                </CTableRow>
              </CTableHead>
              <CTableBody>
                <CTableRow v-for="item in tableExample" :key="item.user.name">
                  <CTableDataCell class="text-center">
                    <CAvatar size="md" :src="item.avatar.src" :status="item.avatar.status" />
                  </CTableDataCell>
                  <CTableDataCell>
                    <div>{{ item.user.name }}</div>
                    <div class="small text-body-secondary text-nowrap">
                      <span>{{ item.user.new ? 'New' : 'Recurring' }}</span> |
                      {{ item.user.registered }}
                    </div>
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                    <CIcon size="xl" :name="item.country.flag" :title="item.country.name" />
                  </CTableDataCell>
                  <CTableDataCell>
                    <div class="d-flex justify-content-between align-items-baseline">
                      <div class="fw-semibold">{{ item.usage.value }}%</div>
                      <div class="text-nowrap text-body-secondary small ms-3">
                        {{ item.usage.period }}
                      </div>
                    </div>
                    <CProgress thin :color="item.usage.color" :value="item.usage.value" />
                  </CTableDataCell>
                  <CTableDataCell class="text-center">
                    <CIcon size="xl" :icon="item.payment.icon" />
                  </CTableDataCell>
                  <CTableDataCell>
                    <div class="small text-body-secondary">Last login</div>
                    <div class="fw-semibold text-nowrap">
                      {{ item.activity }}
                    </div>
                  </CTableDataCell>
                </CTableRow>
              </CTableBody>
            </CTable>
          </CCardBody>
        </CCard>
      </CCol>
    </CRow>
  </div>
</template>
