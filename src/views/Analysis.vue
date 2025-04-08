<template>
  <div class="analysis-container">
    <!-- 取消率预测表单 -->
    <el-card class="prediction-form">
      <template #header>
        <div class="card-header">
          <span>取消率预测</span>
        </div>
      </template>
      <el-form :model="predictionForm" label-width="100px">
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item label="时间范围">
              <el-date-picker
                v-model="predictionForm.dateRange"
                type="daterange"
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
                value-format="YYYY-MM-DD"
              />
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="年龄范围">
              <el-slider
                v-model="predictionForm.ageRange"
                range
                :min="18"
                :max="80"
              />
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="性别">
              <el-radio-group v-model="predictionForm.gender">
                <el-radio label="all">全部</el-radio>
                <el-radio label="male">男性</el-radio>
                <el-radio label="female">女性</el-radio>
              </el-radio-group>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row :gutter="20">
          <el-col :span="8">
            <el-form-item label="预定渠道">
              <el-select v-model="predictionForm.channel" placeholder="请选择">
                <el-option label="全部" value="all" />
                <el-option label="官网" value="website" />
                <el-option label="APP" value="app" />
                <el-option label="OTA" value="ota" />
                <el-option label="电话" value="phone" />
              </el-select>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="金额范围">
              <el-slider
                v-model="predictionForm.priceRange"
                range
                :min="0"
                :max="5000"
                :step="100"
              />
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="地域">
              <el-select v-model="predictionForm.region" placeholder="请选择">
                <el-option label="全部" value="all" />
                <el-option label="北京" value="beijing" />
                <el-option label="上海" value="shanghai" />
                <el-option label="广东" value="guangdong" />
                <el-option label="浙江" value="zhejiang" />
                <el-option label="江苏" value="jiangsu" />
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-form-item>
          <el-button type="primary" @click="predictCancellation">生成预测</el-button>
        </el-form-item>
      </el-form>
    </el-card>

    <!-- 预测结果 -->
    <el-row :gutter="20" class="mt-20">
      <el-col :span="12">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>月度取消率趋势</span>
            </div>
          </template>
          <div ref="monthlyChart" class="chart"></div>
        </el-card>
      </el-col>
      <el-col :span="12">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>预测结果分析</span>
            </div>
          </template>
          <div class="result-content">
            <div class="result-value">
              <span class="label">预测取消率：</span>
              <span class="value">{{ predictionResult.rate }}%</span>
            </div>
            <div class="result-reason">
              <span class="label">预测理由：</span>
              <p>{{ predictionResult.reason }}</p>
            </div>
          </div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import * as echarts from 'echarts'

const monthlyChart = ref(null)

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
  
  // 更新月度趋势图
  updateMonthlyChart()
}

// 更新月度趋势图
const updateMonthlyChart = () => {
  const chartInstance = echarts.init(monthlyChart.value)
  const option = {
    tooltip: {
      trigger: 'axis',
      formatter: '{b}<br/>{a}: {c}%'
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
      data: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月']
    },
    yAxis: {
      type: 'value',
      name: '取消率(%)',
      axisLabel: {
        formatter: '{value}%'
      }
    },
    series: [
      {
        name: '实际取消率',
        type: 'line',
        data: [15, 18, 20, 16, 19, 22, 25, 23, 20, 18, 16, 14],
        itemStyle: {
          color: '#409EFF'
        }
      },
      {
        name: '预测取消率',
        type: 'line',
        data: [null, null, null, null, null, null, null, null, null, null, null, predictionResult.value.rate],
        itemStyle: {
          color: '#F56C6C'
        },
        lineStyle: {
          type: 'dashed'
        }
      }
    ]
  }
  chartInstance.setOption(option)
}

onMounted(() => {
  updateMonthlyChart()
})
</script>

<style scoped>
.analysis-container {
  padding: 20px;
  min-height: calc(100vh - 60px);
}

.prediction-form {
  margin-bottom: 20px;
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
  font-weight: bold;
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

:deep(.el-form-item__label) {
  font-weight: 500;
}
</style> 