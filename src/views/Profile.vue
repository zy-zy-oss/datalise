<template>
  <div class="profile-container">


    <el-row :gutter="20" class="mt-20">
      <!-- 年龄分布 -->
      <el-col :span="8">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>年龄分布</span>
            </div>
          </template>
          <div ref="ageChart" class="chart"></div>
        </el-card>
      </el-col>
      <!-- 性别分布 -->
      <el-col :span="8">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>性别分布</span>
            </div>
          </template>
          <div ref="genderChart" class="chart"></div>
        </el-card>
      </el-col>
      <!-- 取消率预测 -->
      <el-col :span="8">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>取消频率</span>
            </div>
          </template>
          <div ref="cancelChart" class="chart"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-row :gutter="20" class="mt-20">
      <!-- 地域分布 -->
      <el-col :span="12">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>地域分布</span>
            </div>
          </template>
          <div ref="mapChart" class="chart"></div>
        </el-card>
      </el-col>
      <!-- 预订习惯 -->
      <el-col :span="12">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>预订习惯</span>
            </div>
          </template>
          <div ref="bookingChart" class="chart"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-row :gutter="20" class="mt-20">
      <!-- 消费水平 -->
      <el-col :span="12">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>消费水平</span>
            </div>
          </template>
          <div ref="consumptionChart" class="chart"></div>
        </el-card>
      </el-col>
      <!-- 提前取消率 -->
      <el-col :span="12">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>提前取消率</span>
            </div>
          </template>
          <div ref="earlyCancelChart" class="chart"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-card class="marketing-tools">
      <template #header>
        <div class="card-header">
          <span>营销工具</span>
        </div>
      </template>
      <el-row :gutter="20">
        <el-col :span="8" v-for="(tool, index) in marketingTools" :key="index">
          <el-card class="tool-card" shadow="hover">
            <template #header>
              <div class="tool-header">
                <el-icon><component :is="tool.icon" /></el-icon>
                <span>{{ tool.name }}</span>
              </div>
            </template>
            <div class="tool-content">
              <div class="tool-description">{{ tool.description }}</div>
              <div class="tool-metrics">
                <div class="metric">
                  <span class="label">转化率</span>
                  <span class="value">{{ tool.conversionRate }}%</span>
                </div>
                <div class="metric">
                  <span class="label">ROI</span>
                  <span class="value">{{ tool.roi }}</span>
                </div>
              </div>
            </div>
          </el-card>
        </el-col>
      </el-row>
    </el-card>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import * as echarts from 'echarts'
import 'echarts-liquidfill'
import { Promotion, Connection, ChatDotRound } from '@element-plus/icons-vue'

const ageChart = ref(null)
const genderChart = ref(null)
const cancelChart = ref(null)
const mapChart = ref(null)
const bookingChart = ref(null)
const consumptionChart = ref(null)
const earlyCancelChart = ref(null)

const marketingTools = [
  {
    name: '精准推送',
    icon: 'Promotion',
    description: '基于用户画像的个性化内容推送',
    conversionRate: 15.6,
    roi: '1:4.2'
  },
  {
    name: '社交营销',
    icon: 'Connection',
    description: '社交媒体平台营销活动',
    conversionRate: 12.3,
    roi: '1:3.8'
  },
  {
    name: '客服互动',
    icon: 'ChatDotRound',
    description: '智能客服系统互动营销',
    conversionRate: 18.9,
    roi: '1:5.1'
  }
]

// 中国地图数据
const chinaData = [
  { name: '北京', value: 2000 },
  { name: '上海', value: 1800 },
  { name: '广东', value: 1500 },
  { name: '浙江', value: 1200 },
  { name: '江苏', value: 1000 }
]

// 性别分布数据
const genderData = [
  { value: 0.6, name: '男性' },
  { value: 0.4, name: '女性' }
]

// 预测表单数据
const predictionForm = ref({
  dateRange: [],
  ageRange: [18, 80],
  gender: 'all',
  channel: 'all',
  priceRange: [0, 5000],
  region: 'all'
})

// 预测结果
const showPredictionResult = ref(false)
const predictionResult = ref({
  rate: 0,
  reason: ''
})

// 生成预测
const predictCancellation = () => {
  // 模拟预测结果
  predictionResult.value = {
    rate: Math.random() * 30 + 10, // 10-40% 的随机取消率
    reason: `根据所选条件分析：
1. 该时间段内预订量较稳定
2. 目标客户群体消费能力较强
3. 选择渠道的客户忠诚度较高
4. 该地区客户取消率普遍较低
综合以上因素，预测取消率较低。`
  }
  showPredictionResult.value = true
}

onMounted(() => {
  // 年龄分布饼图
  const ageChartInstance = echarts.init(ageChart.value)
  ageChartInstance.setOption({
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
        { value: 1048, name: '18-25岁' },
        { value: 735, name: '26-35岁' },
        { value: 580, name: '36-45岁' },
        { value: 484, name: '46-55岁' },
        { value: 300, name: '56岁以上' }
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

  // 性别分布液体图
  const genderChartInstance = echarts.init(genderChart.value)
  const genderOption = {
    title: {
      text: '性别分布',
      left: 'center',
      top: 20
    },
    series: [{
      type: 'liquidFill',
      data: [0.6, 0.4],
      radius: '80%',
      center: ['50%', '50%'],
      label: {
        formatter: function(param) {
          return '男性\n60%'
        },
        fontSize: 20,
        color: '#333',
        position: ['50%', '50%']
      },
      outline: {
        show: false
      },
      backgroundStyle: {
        color: '#fff'
      },
      itemStyle: {
        color: '#409EFF'
      },
      emphasis: {
        itemStyle: {
          opacity: 0.8
        }
      }
    }]
  }
  genderChartInstance.setOption(genderOption)

  // 取消频率环形图
  const cancelChartInstance = echarts.init(cancelChart.value)
  cancelChartInstance.setOption({
    title: {
      text: '总客户数\n10,000',
      left: 'center',
      top: 'center',
      textStyle: {
        fontSize: 14
      }
    },
    tooltip: {
      trigger: 'item'
    },
    series: [{
      type: 'pie',
      radius: ['50%', '70%'],
      avoidLabelOverlap: false,
      label: {
        show: false
      },
      emphasis: {
        label: {
          show: true,
          fontSize: '20',
          fontWeight: 'bold'
        }
      },
      labelLine: {
        show: false
      },
      data: [
        { value: 3000, name: '经常取消' },
        { value: 4000, name: '偶尔取消' },
        { value: 3000, name: '从不取消' }
      ]
    }]
  })

  // 地域分布地图
  const mapChartInstance = echarts.init(mapChart.value)
  const option = {
    title: {
      text: '中国客户分布',
      left: 'center'
    },
    tooltip: {
      trigger: 'item',
      formatter: '{b}: {c} 人'
    },
    visualMap: {
      min: 0,
      max: 2000,
      left: 'right',
      top: 'bottom',
      text: ['高', '低'],
      calculable: true,
      inRange: {
        color: ['#e0f3f8', '#4575b4']
      }
    },
    toolbox: {
      show: true,
      left: 'left',
      top: 'top',
      feature: {
        dataView: { readOnly: false },
        restore: {},
        saveAsImage: {}
      }
    },
    series: [
      {
        name: '客户数量',
        type: 'map',
        map: 'china',
        roam: true,
        emphasis: {
          label: {
            show: true
          }
        },
        data: chinaData
      }
    ]
  }

  // 加载中国地图数据
  fetch('https://geo.datav.aliyun.com/areas_v3/bound/100000_full.json')
    .then(response => response.json())
    .then(data => {
      echarts.registerMap('china', data)
      mapChartInstance.setOption(option)
    })
    .catch(error => {
      console.error('加载地图数据失败:', error)
    })

  // 预定习惯柱状图
  const bookingChartInstance = echarts.init(bookingChart.value)
  bookingChartInstance.setOption({
    tooltip: {
      trigger: 'axis',
      axisPointer: {
        type: 'shadow'
      }
    },
    grid: {
      left: '3%',
      right: '4%',
      bottom: '3%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      data: ['提前1天', '提前3天', '提前1周', '提前2周', '提前1月']
    },
    yAxis: {
      type: 'value'
    },
    series: [{
      data: [120, 200, 150, 80, 70],
      type: 'bar',
      showBackground: true,
      backgroundStyle: {
        color: 'rgba(180, 180, 180, 0.2)'
      }
    }]
  })

  // 消费行为柱状图
  const consumptionChartInstance = echarts.init(consumptionChart.value)
  consumptionChartInstance.setOption({
    tooltip: {
      trigger: 'axis',
      axisPointer: {
        type: 'shadow'
      }
    },
    legend: {
      data: ['低消费', '中消费', '高消费']
    },
    grid: {
      left: '3%',
      right: '4%',
      bottom: '3%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      data: ['标准房', '豪华房', '套房', '别墅']
    },
    yAxis: {
      type: 'value'
    },
    series: [
      {
        name: '低消费',
        type: 'bar',
        stack: 'total',
        data: [320, 302, 301, 334]
      },
      {
        name: '中消费',
        type: 'bar',
        stack: 'total',
        data: [120, 132, 101, 134]
      },
      {
        name: '高消费',
        type: 'bar',
        stack: 'total',
        data: [220, 182, 191, 234]
      }
    ]
  })

  // 提前取消占比柱状图
  const earlyCancelChartInstance = echarts.init(earlyCancelChart.value)
  earlyCancelChartInstance.setOption({
    tooltip: {
      trigger: 'axis',
      axisPointer: {
        type: 'shadow'
      }
    },
    grid: {
      left: '3%',
      right: '4%',
      bottom: '3%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      data: ['1小时内', '1-3小时', '3-6小时', '6-12小时', '12-24小时', '24小时以上']
    },
    yAxis: {
      type: 'value',
      name: '取消率(%)'
    },
    series: [{
      data: [5, 8, 12, 15, 20, 25],
      type: 'bar',
      itemStyle: {
        color: '#F56C6C'
      }
    }]
  })
})
</script>

<style scoped>
.profile-container {
  padding: 20px;
  min-height: calc(100vh - 60px);
}

.chart-card {
  height: 400px;
  margin-bottom: 20px;
  display: flex;
  flex-direction: column;
}

.chart {
  flex: 1;
  width: 100%;
}

.mt-20 {
  margin-top: 20px;
}

:deep(.el-card__body) {
  height: 100%;
  padding: 20px;
  display: flex;
  flex-direction: column;
}

:deep(.el-card__header) {
  padding: 15px 20px;
  border-bottom: 1px solid #EBEEF5;
}

:deep(.el-menu) {
  border-right: none;
}

:deep(.el-menu-item) {
  height: 50px;
  line-height: 50px;
}

:deep(.el-menu-item.is-active) {
  background-color: #ecf5ff;
  color: #409EFF;
}

:deep(.el-menu-item:hover) {
  background-color: #f5f7fa;
}

:deep(.el-menu-item .el-icon) {
  margin-right: 8px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.marketing-tools {
  margin-top: 20px;
}

.tool-card {
  margin-bottom: 20px;
}

.tool-header {
  display: flex;
  align-items: center;
  gap: 8px;
}

.tool-content {
  padding: 10px 0;
}

.tool-description {
  margin-bottom: 15px;
  color: #666;
}

.tool-metrics {
  display: flex;
  justify-content: space-between;
}

.metric {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.metric .label {
  font-size: 12px;
  color: #666;
}

.metric .value {
  font-size: 18px;
  font-weight: bold;
  color: #409EFF;
}

.prediction-form {
  margin-bottom: 20px;
}

.prediction-result {
  margin-bottom: 20px;
}

.result-content {
  padding: 20px;
}

.result-value {
  margin-bottom: 20px;
  font-size: 18px;
}

.result-value .label {
  color: #606266;
}

.result-value .value {
  color: #409EFF;
  font-weight: bold;
  font-size: 24px;
  margin-left: 10px;
}

.result-reason {
  color: #606266;
  line-height: 1.6;
}

.result-reason .label {
  font-weight: bold;
  margin-bottom: 10px;
  display: block;
}

.result-reason p {
  margin: 0;
  white-space: pre-line;
}

:deep(.el-card__header) {
  padding: 15px 20px;
  border-bottom: 1px solid #EBEEF5;
  font-weight: bold;
}

:deep(.el-form-item__label) {
  font-weight: 500;
}
</style> 