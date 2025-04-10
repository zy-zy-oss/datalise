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

    <el-row :gutter="20" class="chart-row">
      <!-- 堆积柱状图 -->
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>房型与餐饮类型比例</span>
            </div>
          </template>
          <div ref="stackedBarChart" class="chart"></div>
        </el-card>
      </el-col>
      <!-- 折线图 -->
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>入住年月与预定数量趋势</span>
            </div>
          </template>
          <div ref="trendLineChart" class="chart"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-row :gutter="20" class="mt-20">
      <!-- 市场细分饼图 -->
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>市场细分类型（回头客与非回头客）</span>
            </div>
          </template>
          <div ref="marketSegmentChart" class="chart"></div>
        </el-card>
      </el-col>
      <!-- 点状图 -->
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>回头客取消订单次数分析</span>
            </div>
          </template>
          <div ref="cancellationAnalysisChart" class="chart"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-row :gutter="20" class="mt-20">
      <!-- 价格与总住宿晚数折线图 -->
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>价格与总住宿晚数关系</span>
            </div>
          </template>
          <div ref="priceVsNightsChart" class="chart"></div>
        </el-card>
      </el-col>
      <!-- 散点图 -->
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>价格与周内、周末住宿晚数</span>
            </div>
          </template>
          <div ref="weekendVsWeekdayChart" class="chart"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-row :gutter="20" class="mt-20">
      <!-- 堆积柱状图：客户群体变化 -->
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>客户群体变化（回头客与新客）</span>
            </div>
          </template>
          <div ref="customerGroupChangeChart" class="chart"></div>
        </el-card>
      </el-col>
      <!-- 折线图：不同价格区间的预定数量 -->
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>不同价格区间的预定数量</span>
            </div>
          </template>
          <div ref="priceRangeBookingChart" class="chart"></div>
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
const stackedBarChart = ref(null)
const trendLineChart = ref(null)
const marketSegmentChart = ref(null)
const cancellationAnalysisChart = ref(null)
const priceVsNightsChart = ref(null)
const weekendVsWeekdayChart = ref(null)
const customerGroupChangeChart = ref(null)
const priceRangeBookingChart = ref(null)

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

  // 堆积柱状图
  const stackedChartInstance = echarts.init(stackedBarChart.value)
  stackedChartInstance.setOption({
    tooltip: {
      trigger: 'axis'
    },
    legend: {
      data: ['房型', '餐饮类型']
    },
    xAxis: {
      type: 'category',
      data: ['房型1', '房型2', '房型3']
    },
    yAxis: {
      type: 'value'
    },
    series: [
      {
        name: '房型',
        type: 'bar',
        stack: '总量',
        data: [120, 200, 150]
      },
      {
        name: '餐饮类型',
        type: 'bar',
        stack: '总量',
        data: [80, 100, 90]
      }
    ]
  })

  // 折线图
  const trendChartInstance = echarts.init(trendLineChart.value)
  trendChartInstance.setOption({
    tooltip: {
      trigger: 'axis'
    },
    xAxis: {
      type: 'category',
      data: ['2023-01', '2023-02', '2023-03', '2023-04', '2023-05']
    },
    yAxis: {
      type: 'value'
    },
    series: [{
      name: '预定数量',
      type: 'line',
      data: [120, 200, 150, 80, 70]
    }]
  })

  // 市场细分饼图
  const marketChartInstance = echarts.init(marketSegmentChart.value)
  marketChartInstance.setOption({
    tooltip: {
      trigger: 'item'
    },
    series: [{
      type: 'pie',
      radius: '50%',
      data: [
        { value: 335, name: '在线预订' },
        { value: 310, name: '离线预订' }
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

  // 点状图
  const cancellationChartInstance = echarts.init(cancellationAnalysisChart.value)
  cancellationChartInstance.setOption({
    tooltip: {
      trigger: 'axis'
    },
    xAxis: {
      type: 'value',
      name: '取消订单次数'
    },
    yAxis: {
      type: 'value',
      name: '客人数量'
    },
    series: [{
      name: '回头客',
      type: 'scatter',
      data: [[1, 10], [2, 20], [3, 30], [4, 40]]
    }]
  })

  // 价格与总住宿晚数折线图
  const priceNightsChartInstance = echarts.init(priceVsNightsChart.value)
  priceNightsChartInstance.setOption({
    tooltip: {
      trigger: 'axis'
    },
    xAxis: {
      type: 'category',
      data: ['价格区间1', '价格区间2', '价格区间3']
    },
    yAxis: {
      type: 'value',
      name: '总住宿晚数'
    },
    series: [{
      name: '总住宿晚数',
      type: 'line',
      data: [120, 200, 150]
    }]
  })

  // 散点图
  const weekendWeekdayChartInstance = echarts.init(weekendVsWeekdayChart.value)
  weekendWeekdayChartInstance.setOption({
    tooltip: {
      trigger: 'axis'
    },
    xAxis: {
      type: 'value',
      name: '价格区间'
    },
    yAxis: {
      type: 'value',
      name: '住宿数量'
    },
    series: [
      {
        name: '周内住宿数量',
        type: 'scatter',
        data: [[1, 50], [2, 80], [3, 60]]
      },
      {
        name: '周末住宿数量',
        type: 'scatter',
        data: [[1, 30], [2, 40], [3, 50]]
      }
    ]
  })

  // 堆积柱状图：客户群体变化
  const customerGroupChartInstance = echarts.init(customerGroupChangeChart.value)
  customerGroupChartInstance.setOption({
    tooltip: {
      trigger: 'axis'
    },
    legend: {
      data: ['回头客', '新客']
    },
    xAxis: {
      type: 'category',
      data: ['2023-01', '2023-02', '2023-03', '2023-04']
    },
    yAxis: {
      type: 'value'
    },
    series: [
      {
        name: '回头客',
        type: 'bar',
        stack: '客户群体',
        data: [120, 200, 150, 80]
      },
      {
        name: '新客',
        type: 'bar',
        stack: '客户群体',
        data: [80, 100, 90, 70]
      }
    ]
  })

  // 折线图：不同价格区间的预定数量
  const priceRangeChartInstance = echarts.init(priceRangeBookingChart.value)
  priceRangeChartInstance.setOption({
    tooltip: {
      trigger: 'axis'
    },
    xAxis: {
      type: 'category',
      data: ['价格区间1', '价格区间2', '价格区间3']
    },
    yAxis: {
      type: 'value',
      name: '预定数量'
    },
    series: [{
      name: '预定数量',
      type: 'line',
      data: [120, 200, 150]
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

.chart {
  height: 300px;
  width: 100%;
}
</style> 