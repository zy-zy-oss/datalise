<template>
  <div class="dynamic-pricing">
    <el-row :gutter="20">
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>季节性需求</span>
            </div>
          </template>
          <div ref="seasonDemandChart" class="chart"></div>
        </el-card>
      </el-col>
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>各月份客流量</span>
            </div>
          </template>
          <div ref="monthlyTrafficChart" class="chart"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-row :gutter="20" class="mt-20">
      <el-col :span="24">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>各房型需求</span>
            </div>
          </template>
          <div ref="roomTypeDemandChart" class="chart"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-form class="mt-20" :model="form">
      <el-form-item label="季节">
        <el-select v-model="form.season" placeholder="选择季节">
          <el-option label="春季" value="spring"></el-option>
          <el-option label="夏季" value="summer"></el-option>
          <el-option label="秋季" value="autumn"></el-option>
          <el-option label="冬季" value="winter"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="月份">
        <el-select v-model="form.month" placeholder="选择月份">
          <el-option v-for="month in months" :key="month.value" :label="month.label" :value="month.value"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="房型">
        <el-select v-model="form.roomType" placeholder="选择房型">
          <el-option label="标准间" value="standard"></el-option>
          <el-option label="豪华房" value="deluxe"></el-option>
          <el-option label="套房" value="suite"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="原价">
        <el-input v-model="form.originalPrice" placeholder="输入原价"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="generateChart">生成图表</el-button>
      </el-form-item>
    </el-form>

    <div ref="suggestedPriceChart" class="chart mt-20"></div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import * as echarts from 'echarts'

const form = ref({
  season: '',
  month: '',
  roomType: '',
  originalPrice: ''
})

const months = [
  { label: '一月', value: '1' },
  { label: '二月', value: '2' },
  { label: '三月', value: '3' },
  { label: '四月', value: '4' },
  { label: '五月', value: '5' },
  { label: '六月', value: '6' },
  { label: '七月', value: '7' },
  { label: '八月', value: '8' },
  { label: '九月', value: '9' },
  { label: '十月', value: '10' },
  { label: '十一月', value: '11' },
  { label: '十二月', value: '12' }
]

const seasonDemandChart = ref(null)
const monthlyTrafficChart = ref(null)
const roomTypeDemandChart = ref(null)
const suggestedPriceChart = ref(null)

onMounted(() => {
  // 初始化季节性需求饼图
  const seasonChartInstance = echarts.init(seasonDemandChart.value)
  seasonChartInstance.setOption({
    tooltip: {
      trigger: 'item'
    },
    series: [{
      type: 'pie',
      radius: '50%',
      data: [
        { value: 335, name: '春季' },
        { value: 310, name: '夏季' },
        { value: 234, name: '秋季' },
        { value: 135, name: '冬季' }
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

  // 初始化各月份客流量环形图
  const monthlyChartInstance = echarts.init(monthlyTrafficChart.value)
  monthlyChartInstance.setOption({
    tooltip: {
      trigger: 'item'
    },
    series: [{
      type: 'pie',
      radius: ['40%', '70%'],
      data: [
        { value: 1048, name: '一月' },
        { value: 735, name: '二月' },
        { value: 580, name: '三月' },
        { value: 484, name: '四月' },
        { value: 300, name: '五月' },
        { value: 400, name: '六月' },
        { value: 500, name: '七月' },
        { value: 600, name: '八月' },
        { value: 700, name: '九月' },
        { value: 800, name: '十月' },
        { value: 900, name: '十一月' },
        { value: 1000, name: '十二月' }
      ]
    }]
  })

  // 初始化各房型需求柱形图
  const roomTypeChartInstance = echarts.init(roomTypeDemandChart.value)
  roomTypeChartInstance.setOption({
    tooltip: {
      trigger: 'axis'
    },
    xAxis: {
      type: 'category',
      data: ['标准间', '豪华房', '套房']
    },
    yAxis: {
      type: 'value'
    },
    series: [{
      data: [120, 200, 150],
      type: 'bar'
    }]
  })
})

const generateChart = () => {
  // 生成原值和建议值图表的逻辑
  const suggestedPriceChartInstance = echarts.init(suggestedPriceChart.value)
  suggestedPriceChartInstance.setOption({
    tooltip: {
      trigger: 'axis'
    },
    legend: {
      data: ['原价', '建议价']
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
        name: '原价',
        type: 'bar',
        data: [form.value.originalPrice, form.value.originalPrice * 1.1, form.value.originalPrice * 1.2]
      },
      {
        name: '建议价',
        type: 'bar',
        data: [form.value.originalPrice * 0.9, form.value.originalPrice * 1.0, form.value.originalPrice * 1.1]
      }
    ]
  })
}
</script>

<style scoped>
.dynamic-pricing {
  padding: 20px;
}

.chart {
  height: 300px;
  width: 100%;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style> 