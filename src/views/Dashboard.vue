<template>
  <div class="dashboard">
    <el-row :gutter="20">
      <el-col :span="6" v-for="(card, index) in cards" :key="index">
        <el-card class="data-card">
          <template #header>
            <div class="card-header">
              <span>{{ card.title }}</span>
              <el-icon><component :is="card.icon" /></el-icon>
            </div>
          </template>
          <div class="card-content">
            <div class="value">{{ card.value }}</div>
            <div class="trend" :class="card.trend > 0 ? 'up' : 'down'">
              {{ card.trend > 0 ? '+' : '' }}{{ card.trend }}%
            </div>
          </div>
        </el-card>
      </el-col>
    </el-row>

    <el-row :gutter="20" class="chart-row">
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>销售趋势</span>
            </div>
          </template>
          <div ref="salesChart" style="height: 300px"></div>
        </el-card>
      </el-col>
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>客户分布</span>
            </div>
          </template>
          <div ref="customerChart" style="height: 300px"></div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import * as echarts from 'echarts'
import { TrendCharts, User, Money, ShoppingCart } from '@element-plus/icons-vue'

const cards = [
  { title: '总销售额', value: '¥126,560', trend: 12, icon: 'Money' },
  { title: '访问量', value: '8,846', trend: 8, icon: 'User' },
  { title: '订单量', value: '6,560', trend: -2, icon: 'ShoppingCart' },
  { title: '转化率', value: '78%', trend: 5, icon: 'TrendCharts' }
]

const salesChart = ref(null)
const customerChart = ref(null)

onMounted(() => {
  // 初始化销售趋势图表
  const salesChartInstance = echarts.init(salesChart.value)
  salesChartInstance.setOption({
    tooltip: {
      trigger: 'axis'
    },
    xAxis: {
      type: 'category',
      data: ['1月', '2月', '3月', '4月', '5月', '6月']
    },
    yAxis: {
      type: 'value'
    },
    series: [{
      data: [150, 230, 224, 218, 135, 147],
      type: 'line',
      smooth: true
    }]
  })

  // 初始化客户分布图表
  const customerChartInstance = echarts.init(customerChart.value)
  customerChartInstance.setOption({
    tooltip: {
      trigger: 'item'
    },
    legend: {
      orient: 'vertical',
      left: 'left'
    },
    series: [{
      type: 'pie',
      radius: '50%',
      data: [
        { value: 1048, name: '新客户' },
        { value: 735, name: '老客户' },
        { value: 580, name: '潜在客户' },
        { value: 484, name: '流失客户' }
      ],
      emphasis: {
        itemStyle: {
          shadowBlur: 10,
          shadowOffsetX: 0,
          shadowColor: 'rgba(0, 0, 0, 0.5)'
        }
      }
    }]
  })
})
</script>

<style scoped>
.dashboard {
  padding: 20px;
}

.data-card {
  margin-bottom: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.card-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.value {
  font-size: 24px;
  font-weight: bold;
}

.trend {
  font-size: 14px;
}

.trend.up {
  color: #67c23a;
}

.trend.down {
  color: #f56c6c;
}

.chart-row {
  margin-top: 20px;
}
</style> 