<template>
  <div class="analysis">
    <el-row :gutter="20">
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>客户特征分布</span>
            </div>
          </template>
          <div ref="featureChart" style="height: 400px"></div>
        </el-card>
      </el-col>
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>取消率预测</span>
            </div>
          </template>
          <div ref="cancelChart" style="height: 400px"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-card class="feature-table">
      <template #header>
        <div class="card-header">
          <span>特征重要性排名</span>
        </div>
      </template>
      <el-table :data="featureData" style="width: 100%">
        <el-table-column prop="feature" label="特征" />
        <el-table-column prop="importance" label="重要性" />
        <el-table-column prop="impact" label="影响程度">
          <template #default="scope">
            <el-tag :type="scope.row.impact > 0 ? 'success' : 'danger'">
              {{ scope.row.impact > 0 ? '+' : '' }}{{ scope.row.impact }}
            </el-tag>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import * as echarts from 'echarts'

const featureChart = ref(null)
const cancelChart = ref(null)

const featureData = [
  { feature: '消费频率', importance: 0.85, impact: 0.32 },
  { feature: '客单价', importance: 0.78, impact: 0.28 },
  { feature: '使用时长', importance: 0.65, impact: 0.25 },
  { feature: '投诉次数', importance: 0.58, impact: -0.42 },
  { feature: '活跃度', importance: 0.52, impact: 0.18 }
]

onMounted(() => {
  // 初始化特征分布图表
  const featureChartInstance = echarts.init(featureChart.value)
  featureChartInstance.setOption({
    tooltip: {
      trigger: 'axis',
      axisPointer: {
        type: 'shadow'
      }
    },
    legend: {
      data: ['高价值客户', '普通客户', '低价值客户']
    },
    grid: {
      left: '3%',
      right: '4%',
      bottom: '3%',
      containLabel: true
    },
    xAxis: {
      type: 'value'
    },
    yAxis: {
      type: 'category',
      data: ['消费频率', '客单价', '使用时长', '投诉次数', '活跃度']
    },
    series: [
      {
        name: '高价值客户',
        type: 'bar',
        stack: 'total',
        label: {
          show: true
        },
        emphasis: {
          focus: 'series'
        },
        data: [320, 302, 301, 334, 390]
      },
      {
        name: '普通客户',
        type: 'bar',
        stack: 'total',
        label: {
          show: true
        },
        emphasis: {
          focus: 'series'
        },
        data: [120, 132, 101, 134, 90]
      },
      {
        name: '低价值客户',
        type: 'bar',
        stack: 'total',
        label: {
          show: true
        },
        emphasis: {
          focus: 'series'
        },
        data: [220, 182, 191, 234, 290]
      }
    ]
  })

  // 初始化取消率预测图表
  const cancelChartInstance = echarts.init(cancelChart.value)
  cancelChartInstance.setOption({
    tooltip: {
      trigger: 'axis'
    },
    legend: {
      data: ['实际取消率', '预测取消率']
    },
    grid: {
      left: '3%',
      right: '4%',
      bottom: '3%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      boundaryGap: false,
      data: ['1月', '2月', '3月', '4月', '5月', '6月']
    },
    yAxis: {
      type: 'value'
    },
    series: [
      {
        name: '实际取消率',
        type: 'line',
        data: [12, 13, 10, 13, 9, 11],
        markLine: {
          data: [
            { type: 'average', name: '平均值' }
          ]
        }
      },
      {
        name: '预测取消率',
        type: 'line',
        data: [11, 12, 9, 12, 8, 10],
        markLine: {
          data: [
            { type: 'average', name: '平均值' }
          ]
        }
      }
    ]
  })
})
</script>

<style scoped>
.analysis {
  padding: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.feature-table {
  margin-top: 20px;
}
</style> 