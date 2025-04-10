<template>
  <div class="profile-container">
    <!-- 图表部分 -->
    <el-row :gutter="20">
      <!-- 客户类型柱状图 -->
      <el-col :span="12">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>客户类型分布</span>
            </div>
          </template>
          <div ref="customerTypeChart" class="chart"></div>
        </el-card>
      </el-col>
      <!-- 客户类型分类占比环形图 -->
      <el-col :span="12">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>客户类型占比</span>
            </div>
          </template>
          <div ref="customerTypeRatioChart" class="chart"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-row :gutter="20" class="mt-20">
      <!-- 客户类型与市场来源堆积柱状图 -->
      <el-col :span="24">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>客户类型与市场来源分析</span>
            </div>
          </template>
          <div ref="marketSourceChart" class="chart"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-row :gutter="20" class="mt-20">
      <!-- 客户类型与各种需求的柱形图 -->
      <el-col :span="24">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>客户需求分析</span>
            </div>
          </template>
          <div ref="customerNeedsChart" class="chart"></div>
        </el-card>
      </el-col>
    </el-row>

    <el-row :gutter="20" class="mt-20">
      <!-- 客户类型与入住时间段折线图 -->
      <el-col :span="24">
        <el-card class="chart-card">
          <template #header>
            <div class="card-header">
              <span>入住时间段分析</span>
            </div>
          </template>
          <div ref="checkInTimeChart" class="chart"></div>
        </el-card>
      </el-col>
    </el-row>

    <!-- 客户信息表格 -->
    <el-card class="mt-20">
      <template #header>
        <div class="card-header">
          <span>客户信息列表</span>
        </div>
      </template>
      <el-table :data="customerList" style="width: 100%">
        <el-table-column prop="userId" label="用户ID" width="100" />
        <el-table-column prop="adultCount" label="成人数量" width="100" />
        <el-table-column prop="childCount" label="儿童数量" width="100" />
        <el-table-column prop="nightsNotStayed" label="未住宿晚" width="100" />
        <el-table-column prop="mealPlan" label="饮食套餐" width="150" />
        <el-table-column prop="needsParking" label="是否需要停车" width="150" />
        <el-table-column prop="roomType" label="预定的房型" width="150" />
        <el-table-column prop="bookingTime" label="预定时间" width="150" />
        <el-table-column prop="checkInYear" label="入住年份" width="100" />
        <el-table-column prop="checkInMonth" label="入住月份" width="100" />
        <el-table-column prop="checkInDate" label="入住日期" width="100" />
        <el-table-column prop="venueCategory" label="场细分类" width="150" />
        <el-table-column prop="isReturningCustomer" label="是否为回头客" width="150" />
        <el-table-column prop="canceledBooking" label="取消的预定" width="150" />
        <el-table-column prop="specialNeedsCount" label="特殊需求数" width="150" />
        <el-table-column prop="orderStatus" label="订单状态" width="150" />
        <el-table-column prop="customerType" label="客户类型" width="120" />
        <el-table-column label="操作" width="200">
          <template #default="scope">
            <el-button type="primary" size="small" @click="handleRetention(scope.row)">
              促留
            </el-button>
            <el-button type="danger" size="small" @click="handleDelete(scope.row)">
              删除
            </el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-card>

    <!-- 促留弹窗 -->
    <el-dialog
      v-model="retentionDialogVisible"
      title="客户促留信息"
      width="50%"
    >
      <el-descriptions :column="2" border>
        <el-descriptions-item label="客户姓名">{{ selectedCustomer.name }}</el-descriptions-item>
        <el-descriptions-item label="客户类型">{{ selectedCustomer.customerType }}</el-descriptions-item>
        <el-descriptions-item label="联系电话">{{ selectedCustomer.phone }}</el-descriptions-item>
        <el-descriptions-item label="房型">{{ selectedCustomer.roomType }}</el-descriptions-item>
        <el-descriptions-item label="房型价格">{{ selectedCustomer.roomPrice }}</el-descriptions-item>
      </el-descriptions>
      
      <el-card class="mt-20">
        <template #header>
          <div class="card-header">
            <span>建议挽留话术</span>
          </div>
        </template>
        <div class="retention-script">
          {{ selectedCustomer.retentionScript }}
        </div>
      </el-card>
    </el-dialog>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import * as echarts from 'echarts'
import { ElMessageBox, ElMessage } from 'element-plus'

// 图表引用
const customerTypeChart = ref(null)
const customerTypeRatioChart = ref(null)
const marketSourceChart = ref(null)
const customerNeedsChart = ref(null)
const checkInTimeChart = ref(null)

// 弹窗控制
const retentionDialogVisible = ref(false)
const selectedCustomer = ref({})

const customerList = ref([
  {
    userId: '001',
    adultCount: 2,
    childCount: 1,
    nightsNotStayed: 0,
    mealPlan: '全餐',
    needsParking: '是',
    roomType: '豪华套房',
    bookingTime: '2023-10-01 12:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 1,
    venueCategory: '会议室',
    isReturningCustomer: '是',
    canceledBooking: '否',
    specialNeedsCount: 0,
    orderStatus: '已入住',
    customerType: '重要价值客户',
  },
  {
    userId: '002',
    adultCount: 1,
    childCount: 0,
    nightsNotStayed: 1,
    mealPlan: '早餐',
    needsParking: '否',
    roomType: '标准间',
    bookingTime: '2023-10-02 14:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 2,
    venueCategory: '宴会厅',
    isReturningCustomer: '否',
    canceledBooking: '是',
    specialNeedsCount: 1,
    orderStatus: '已取消',
    customerType: '潜在价值客户',
  },
  {
    userId: '003',
    adultCount: 2,
    childCount: 2,
    nightsNotStayed: 0,
    mealPlan: '全餐',
    needsParking: '是',
    roomType: '家庭房',
    bookingTime: '2023-10-03 10:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 3,
    venueCategory: '游泳池',
    isReturningCustomer: '是',
    canceledBooking: '否',
    specialNeedsCount: 0,
    orderStatus: '已入住',
    customerType: '重要挽留客户',
  },
  {
    userId: '004',
    adultCount: 1,
    childCount: 0,
    nightsNotStayed: 2,
    mealPlan: '无餐',
    needsParking: '否',
    roomType: '单人间',
    bookingTime: '2023-10-04 16:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 4,
    venueCategory: '健身房',
    isReturningCustomer: '否',
    canceledBooking: '否',
    specialNeedsCount: 0,
    orderStatus: '已入住',
    customerType: '一般客户',
  },
  {
    userId: '005',
    adultCount: 2,
    childCount: 1,
    nightsNotStayed: 0,
    mealPlan: '半餐',
    needsParking: '是',
    roomType: '豪华套房',
    bookingTime: '2023-10-05 12:30',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 5,
    venueCategory: '会议室',
    isReturningCustomer: '是',
    canceledBooking: '否',
    specialNeedsCount: 1,
    orderStatus: '已入住',
    customerType: '重要价值客户',
  },
  {
    userId: '006',
    adultCount: 1,
    childCount: 0,
    nightsNotStayed: 1,
    mealPlan: '早餐',
    needsParking: '否',
    roomType: '标准间',
    bookingTime: '2023-10-06 14:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 6,
    venueCategory: '宴会厅',
    isReturningCustomer: '否',
    canceledBooking: '是',
    specialNeedsCount: 0,
    orderStatus: '已取消',
    customerType: '潜在价值客户',
  },
  {
    userId: '007',
    adultCount: 2,
    childCount: 2,
    nightsNotStayed: 0,
    mealPlan: '全餐',
    needsParking: '是',
    roomType: '家庭房',
    bookingTime: '2023-10-07 10:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 7,
    venueCategory: '游泳池',
    isReturningCustomer: '是',
    canceledBooking: '否',
    specialNeedsCount: 0,
    orderStatus: '已入住',
    customerType: '重要挽留客户',
  },
  {
    userId: '008',
    adultCount: 1,
    childCount: 0,
    nightsNotStayed: 2,
    mealPlan: '无餐',
    needsParking: '否',
    roomType: '单人间',
    bookingTime: '2023-10-08 16:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 8,
    venueCategory: '健身房',
    isReturningCustomer: '否',
    canceledBooking: '否',
    specialNeedsCount: 0,
    orderStatus: '已入住',
    customerType: '一般客户',
  },
  {
    userId: '009',
    adultCount: 2,
    childCount: 1,
    nightsNotStayed: 0,
    mealPlan: '半餐',
    needsParking: '是',
    roomType: '豪华套房',
    bookingTime: '2023-10-09 12:30',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 9,
    venueCategory: '会议室',
    isReturningCustomer: '是',
    canceledBooking: '否',
    specialNeedsCount: 1,
    orderStatus: '已入住',
    customerType: '重要价值客户',
  },
  {
    userId: '010',
    adultCount: 1,
    childCount: 0,
    nightsNotStayed: 1,
    mealPlan: '早餐',
    needsParking: '否',
    roomType: '标准间',
    bookingTime: '2023-10-10 14:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 10,
    venueCategory: '宴会厅',
    isReturningCustomer: '否',
    canceledBooking: '否',
    specialNeedsCount: 0,
    orderStatus: '已入住',
    customerType: '潜在价值客户',
  },
  {
    userId: '011',
    adultCount: 2,
    childCount: 2,
    nightsNotStayed: 0,
    mealPlan: '全餐',
    needsParking: '是',
    roomType: '家庭房',
    bookingTime: '2023-10-11 10:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 11,
    venueCategory: '游泳池',
    isReturningCustomer: '是',
    canceledBooking: '否',
    specialNeedsCount: 0,
    orderStatus: '已入住',
    customerType: '重要挽留客户',
  },
  {
    userId: '012',
    adultCount: 1,
    childCount: 0,
    nightsNotStayed: 2,
    mealPlan: '无餐',
    needsParking: '否',
    roomType: '单人间',
    bookingTime: '2023-10-12 16:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 12,
    venueCategory: '健身房',
    isReturningCustomer: '否',
    canceledBooking: '是',
    specialNeedsCount: 0,
    orderStatus: '已取消',
    customerType: '一般客户',
  },
  {
    userId: '013',
    adultCount: 2,
    childCount: 1,
    nightsNotStayed: 0,
    mealPlan: '半餐',
    needsParking: '是',
    roomType: '豪华套房',
    bookingTime: '2023-10-13 12:30',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 13,
    venueCategory: '会议室',
    isReturningCustomer: '是',
    canceledBooking: '否',
    specialNeedsCount: 1,
    orderStatus: '已入住',
    customerType: '重要价值客户',
  },
  {
    userId: '014',
    adultCount: 1,
    childCount: 0,
    nightsNotStayed: 1,
    mealPlan: '早餐',
    needsParking: '否',
    roomType: '标准间',
    bookingTime: '2023-10-14 14:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 14,
    venueCategory: '宴会厅',
    isReturningCustomer: '否',
    canceledBooking: '否',
    specialNeedsCount: 0,
    orderStatus: '已入住',
    customerType: '潜在价值客户',
  },
  {
    userId: '015',
    adultCount: 2,
    childCount: 2,
    nightsNotStayed: 0,
    mealPlan: '全餐',
    needsParking: '是',
    roomType: '家庭房',
    bookingTime: '2023-10-15 10:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 15,
    venueCategory: '游泳池',
    isReturningCustomer: '是',
    canceledBooking: '否',
    specialNeedsCount: 0,
    orderStatus: '已入住',
    customerType: '重要挽留客户',
  },
  {
    userId: '016',
    adultCount: 1,
    childCount: 0,
    nightsNotStayed: 2,
    mealPlan: '无餐',
    needsParking: '否',
    roomType: '单人间',
    bookingTime: '2023-10-16 16:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 16,
    venueCategory: '健身房',
    isReturningCustomer: '否',
    canceledBooking: '否',
    specialNeedsCount: 0,
    orderStatus: '已入住',
    customerType: '一般客户',
  },
  {
    userId: '017',
    adultCount: 2,
    childCount: 1,
    nightsNotStayed: 0,
    mealPlan: '半餐',
    needsParking: '是',
    roomType: '豪华套房',
    bookingTime: '2023-10-17 12:30',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 17,
    venueCategory: '会议室',
    isReturningCustomer: '是',
    canceledBooking: '否',
    specialNeedsCount: 1,
    orderStatus: '已入住',
    customerType: '重要价值客户',
  },
  {
    userId: '018',
    adultCount: 1,
    childCount: 0,
    nightsNotStayed: 1,
    mealPlan: '早餐',
    needsParking: '否',
    roomType: '标准间',
    bookingTime: '2023-10-18 14:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 18,
    venueCategory: '宴会厅',
    isReturningCustomer: '否',
    canceledBooking: '否',
    specialNeedsCount: 0,
    orderStatus: '已入住',
    customerType: '潜在价值客户',
  },
  {
    userId: '019',
    adultCount: 2,
    childCount: 2,
    nightsNotStayed: 0,
    mealPlan: '全餐',
    needsParking: '是',
    roomType: '家庭房',
    bookingTime: '2023-10-19 10:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 19,
    venueCategory: '游泳池',
    isReturningCustomer: '是',
    canceledBooking: '否',
    specialNeedsCount: 0,
    orderStatus: '已入住',
    customerType: '重要挽留客户',
  },
  {
    userId: '020',
    adultCount: 1,
    childCount: 0,
    nightsNotStayed: 2,
    mealPlan: '无餐',
    needsParking: '否',
    roomType: '单人间',
    bookingTime: '2023-10-20 16:00',
    checkInYear: 2023,
    checkInMonth: 10,
    checkInDate: 20,
    venueCategory: '健身房',
    isReturningCustomer: '否',
    canceledBooking: '是',
    specialNeedsCount: 0,
    orderStatus: '已取消',
    customerType: '一般客户',
  }
]);

// 处理促留按钮点击
const handleRetention = (row) => {
  selectedCustomer.value = row
  retentionDialogVisible.value = true
}

// 处理删除按钮点击
const handleDelete = (row) => {
  ElMessageBox.confirm(
    '确定要删除该客户信息吗？',
    '警告',
    {
      confirmButtonText: '确定',
      cancelButtonText: '取消',
      type: 'warning',
    }
  ).then(() => {
    // 这里实现删除逻辑
    ElMessage.success('删除成功')
  })
}

// 初始化图表
onMounted(() => {
  // 客户类型柱状图
  const typeChart = echarts.init(customerTypeChart.value)
  typeChart.setOption({
    tooltip: {
      trigger: 'axis'
    },
    xAxis: {
      type: 'category',
      data: ['一般客户', '潜在价值客户', '重要挽留客户', '重要价值客户']
    },
    yAxis: {
      type: 'value'
    },
    series: [{
      data: [120, 200, 150, 80],
      type: 'bar',
      itemStyle: {
        color: '#409EFF'
      }
    }]
  })

  // 客户类型占比环形图
  const ratioChart = echarts.init(customerTypeRatioChart.value)
  ratioChart.setOption({
    tooltip: {
      trigger: 'item'
    },
    legend: {
      orient: 'vertical',
      left: 'left'
    },
    series: [{
      type: 'pie',
      radius: ['40%', '70%'],
      data: [
        { value: 120, name: '一般客户' },
        { value: 200, name: '潜在价值客户' },
        { value: 150, name: '重要挽留客户' },
        { value: 80, name: '重要价值客户' }
      ]
    }]
  })

  // 客户类型与市场来源堆积柱状图
  const marketChart = echarts.init(marketSourceChart.value)
  marketChart.setOption({
    tooltip: {
      trigger: 'axis',
      axisPointer: {
        type: 'shadow'
      }
    },
    legend: {
      data: ['OTA', '直销', '旅行社', '企业合作']
    },
    xAxis: {
      type: 'category',
      data: ['一般客户', '潜在价值客户', '重要挽留客户', '重要价值客户']
    },
    yAxis: {
      type: 'value'
    },
    series: [
      {
        name: 'OTA',
        type: 'bar',
        stack: 'total',
        data: [320, 302, 301, 334]
      },
      {
        name: '直销',
        type: 'bar',
        stack: 'total',
        data: [120, 132, 101, 134]
      },
      {
        name: '旅行社',
        type: 'bar',
        stack: 'total',
        data: [220, 182, 191, 234]
      },
      {
        name: '企业合作',
        type: 'bar',
        stack: 'total',
        data: [150, 212, 201, 154]
      }
    ]
  })

  // 客户需求分析柱形图
  const needsChart = echarts.init(customerNeedsChart.value)
  needsChart.setOption({
    tooltip: {
      trigger: 'axis',
      axisPointer: {
        type: 'shadow'
      }
    },
    legend: {
      data: ['一般客户', '潜在价值客户', '重要挽留客户', '重要价值客户']
    },
    xAxis: {
      type: 'category',
      data: ['房型需求', '餐饮需求', '年龄段', '特殊需求', '停车需求']
    },
    yAxis: {
      type: 'value'
    },
    series: [
      {
        name: '一般客户',
        type: 'bar',
        data: [320, 302, 301, 334, 390]
      },
      {
        name: '潜在价值客户',
        type: 'bar',
        data: [120, 132, 101, 134, 90]
      },
      {
        name: '重要挽留客户',
        type: 'bar',
        data: [220, 182, 191, 234, 290]
      },
      {
        name: '重要价值客户',
        type: 'bar',
        data: [150, 212, 201, 154, 190]
      }
    ]
  })

  // 入住时间段折线图
  const timeChart = echarts.init(checkInTimeChart.value)
  timeChart.setOption({
    tooltip: {
      trigger: 'axis'
    },
    legend: {
      data: ['一般客户', '潜在价值客户', '重要挽留客户', '重要价值客户']
    },
    xAxis: {
      type: 'category',
      data: ['6:00', '8:00', '10:00', '12:00', '14:00', '16:00', '18:00', '20:00', '22:00']
    },
    yAxis: {
      type: 'value'
    },
    series: [
      {
        name: '一般客户',
        type: 'line',
        data: [120, 132, 101, 134, 90, 230, 210, 120, 80]
      },
      {
        name: '潜在价值客户',
        type: 'line',
        data: [220, 182, 191, 234, 290, 330, 310, 150, 130]
      },
      {
        name: '重要挽留客户',
        type: 'line',
        data: [150, 232, 201, 154, 190, 330, 410, 200, 150]
      },
      {
        name: '重要价值客户',
        type: 'line',
        data: [320, 332, 301, 334, 390, 330, 320, 280, 200]
      }
    ]
  })

  // 监听窗口大小变化，调整图表大小
  window.addEventListener('resize', () => {
    typeChart.resize()
    ratioChart.resize()
    marketChart.resize()
    needsChart.resize()
    timeChart.resize()
  })
})
</script>

<style scoped>
.profile-container {
  padding: 20px;
}

.chart-card {
  margin-bottom: 20px;
}

.chart {
  height: 400px;
  width: 100%;
}

.mt-20 {
  margin-top: 20px;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.retention-script {
  line-height: 1.8;
  color: #606266;
  white-space: pre-line;
}

:deep(.el-card__header) {
  padding: 15px 20px;
  border-bottom: 1px solid #EBEEF5;
  font-weight: bold;
}

:deep(.el-descriptions) {
  margin-bottom: 20px;
}

:deep(.el-table) {
  margin-top: 20px;
}
</style> 