<template>
  <div class="pricing">
    <el-row :gutter="20">
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>价格趋势分析</span>
            </div>
          </template>
          <div ref="priceTrendChart" style="height: 400px"></div>
        </el-card>
      </el-col>
      <el-col :span="12">
        <el-card>
          <template #header>
            <div class="card-header">
              <span>价格敏感度分析</span>
            </div>
          </template>
          <div ref="sensitivityChart" style="height: 400px"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-card class="pricing-suggestions">
      <template #header>
        <div class="card-header">
          <span>调价建议</span>
        </div>
      </template>
      <el-table :data="pricingSuggestions" style="width: 100%">
        <el-table-column prop="product" label="产品" />
        <el-table-column prop="currentPrice" label="当前价格" />
        <el-table-column prop="suggestedPrice" label="建议价格" />
        <el-table-column prop="change" label="变化幅度">
          <template #default="scope">
            <el-tag :type="scope.row.change > 0 ? 'success' : 'danger'">
              {{ scope.row.change > 0 ? '+' : '' }}{{ scope.row.change }}%
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="expectedImpact" label="预期影响">
          <template #default="scope">
            <el-tag :type="scope.row.expectedImpact > 0 ? 'success' : 'danger'">
              {{ scope.row.expectedImpact > 0 ? '+' : '' }}{{ scope.row.expectedImpact }}%
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <template #default="scope">
            <el-button type="primary" size="small" @click="applyPricing(scope.row)">应用</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import * as echarts from 'echarts'
import { ElMessage } from 'element-plus'

const priceTrendChart = ref(null)
const sensitivityChart = ref(null)

const pricingSuggestions = [
  {
    product: '标准套餐',
    currentPrice: 199,
    suggestedPrice: 219,
    change: 10,
    expectedImpact: 15
  },
  {
    product: '高级套餐',
    currentPrice: 399,
    suggestedPrice: 379,
    change: -5,
    expectedImpact: 8
  },
  {
    product: '企业套餐',
    currentPrice: 999,
    suggestedPrice: 949,
    change: -5,
    expectedImpact: 12
  }
]

const applyPricing = (row) => {
  ElMessage.success(`已应用新的价格策略：${row.product} 价格调整为 ${row.suggestedPrice}元`)
}

onMounted(() => {
  // 初始化价格趋势图表
  const priceTrendChartInstance = echarts.init(priceTrendChart.value)
  priceTrendChartInstance.setOption({
    tooltip: {
      trigger: 'axis'
    },
    legend: {
      data: ['标准套餐', '高级套餐', '企业套餐']
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
        name: '标准套餐',
        type: 'line',
        data: [199, 199, 199, 199, 199, 199]
      },
      {
        name: '高级套餐',
        type: 'line',
        data: [399, 399, 399, 399, 399, 399]
      },
      {
        name: '企业套餐',
        type: 'line',
        data: [999, 999, 999, 999, 999, 999]
      }
    ]
  })

  // 初始化价格敏感度分析图表
  const sensitivityChartInstance = echarts.init(sensitivityChart.value)
  sensitivityChartInstance.setOption({
    tooltip: {
      trigger: 'axis',
      axisPointer: {
        type: 'shadow'
      }
    },
    legend: {
      data: ['价格敏感度', '购买意愿']
    },
    grid: {
      left: '3%',
      right: '4%',
      bottom: '3%',
      containLabel: true
    },
    xAxis: {
      type: 'category',
      data: ['标准套餐', '高级套餐', '企业套餐']
    },
    yAxis: [
      {
        type: 'value',
        name: '敏感度',
        position: 'left'
      },
      {
        type: 'value',
        name: '购买意愿',
        position: 'right'
      }
    ],
    series: [
      {
        name: '价格敏感度',
        type: 'bar',
        data: [0.8, 0.6, 0.4]
      },
      {
        name: '购买意愿',
        type: 'line',
        yAxisIndex: 1,
        data: [0.7, 0.5, 0.3]
      }
    ]
  })
})
</script>

<style scoped>
.pricing {
  padding: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.pricing-suggestions {
  margin-top: 20px;
}
</style> 