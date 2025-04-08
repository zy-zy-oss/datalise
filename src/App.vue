<template>
  <div class="app-container">
    <el-container>
      <el-header>
        <div class="header-content">
          <div class="logo">
            <svg t="1744127702453" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="4753" width="32" height="32">
              <path d="M512 512m-448 0a448 448 0 1 0 896 0 448 448 0 1 0-896 0Z" fill="#4565ED" p-id="4754"></path>
              <path d="M512 512m-448 0a448 448 0 1 0 896 0 448 448 0 1 0-896 0Z" fill="#D9EDFF" p-id="4755"></path>
              <path d="M885.1 760H138.9C219.1 880.6 356.3 960 512 960s292.9-79.4 373.1-200z" fill="#0285FF" p-id="4756"></path>
              <path d="M773.1 760h-240V392h220c11 0 20 9 20 20v348z" fill="#88C6FF" p-id="4757"></path>
              <path d="M662.3 759.7h-300v-440c0-11 9-20 20-20h260c11 0 20 9 20 20v440z" fill="#0285FF" p-id="4758"></path>
              <path d="M415 350h70v42h-70zM535 350h70v42h-70zM415 422h70v42h-70zM535 422h70v42h-70zM415 494h70v42h-70zM535 494h70v42h-70zM415 566h70v42h-70zM535 566h70v42h-70zM415 638h70v42h-70zM535 638h70v42h-70zM685 425h60v36h-60zM685 497h60v36h-60zM685 569h60v36h-60zM685 641h60v36h-60z" fill="#FFEC16" p-id="4759"></path>
              <path d="M220.2 580h20v180h-20z" fill="#88C6FF" p-id="4760"></path>
              <path d="M161.8 611.2l68.4-62.2 68.3 62.2z" fill="#0285FF" p-id="4761"></path>
              <path d="M155.6 651.6l74.6-67.9 74.6 67.9z" fill="#0285FF" p-id="4762"></path>
              <path d="M147.8 696.1l82.4-75 82.3 75z" fill="#0285FF" p-id="4763"></path>
            </svg>
            <span class="logo-text">酒店数据价值挖掘平台</span>
          </div>
          <div class="user-info">
            <el-dropdown @command="handleCommand">
              <span class="el-dropdown-link">
                <el-avatar :size="32" src="https://cube.elemecdn.com/3/7c/3ea6beec64369c2642b92c6726f1epng.png" />
                <span class="username">管理员</span>
                <el-icon class="el-icon--right"><arrow-down /></el-icon>
              </span>
              <template #dropdown>
                <el-dropdown-menu>
                  <el-dropdown-item command="profile">个人中心</el-dropdown-item>
                  <el-dropdown-item command="settings">系统设置</el-dropdown-item>
                  <el-dropdown-item divided command="logout">退出登录</el-dropdown-item>
                </el-dropdown-menu>
              </template>
            </el-dropdown>
          </div>
        </div>
      </el-header>
      <el-container>
        <el-aside width="200px">
          <el-menu
            :default-active="activeMenu"
            class="el-menu-vertical"
            :router="true"
          >
            <el-menu-item index="/dashboard">
              <el-icon><DataLine /></el-icon>
              <span>智能数据看板</span>
            </el-menu-item>
            <el-menu-item index="/analysis">
              <el-icon><TrendCharts /></el-icon>
              <span>客户特征分析与取消率预测</span>
            </el-menu-item>
            <el-menu-item index="/profile">
              <el-icon><User /></el-icon>
              <span>客户画像与营销工具</span>
            </el-menu-item>
            <el-menu-item index="/pricing">
              <el-icon><Money /></el-icon>
              <span>动态调价建议</span>
            </el-menu-item>
          </el-menu>
        </el-aside>
        <el-main>
          <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import { ArrowDown, DataLine, TrendCharts, User, Money } from '@element-plus/icons-vue'
import { ElMessageBox } from 'element-plus'

const router = useRouter()
const route = useRoute()

const activeMenu = computed(() => route.path)

const handleCommand = (command) => {
  switch (command) {
    case 'profile':
      router.push('/profile')
      break
    case 'settings':
      router.push('/settings')
      break
    case 'logout':
      ElMessageBox.confirm('确定要退出登录吗？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 清除用户登录状态
        localStorage.removeItem('isAuthenticated')
        // 跳转到登录页面
        router.push('/login')
      }).catch(() => {})
      break
  }
}
</script>

<style scoped>
.app-container {
  height: 100vh;
  display: flex;
  flex-direction: column;
}

.el-header {
  background-color: #fff;
  box-shadow: 0 1px 4px rgba(0, 21, 41, 0.08);
  padding: 0 20px;
  height: 60px;
  line-height: 60px;
  z-index: 100;
}

.header-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 100%;
}

.logo {
  display: flex;
  align-items: center;
  gap: 10px;
}

.logo-img {
  height: 32px;
}

.logo-text {
  font-size: 18px;
  font-weight: bold;
  color: #409EFF;
}

.user-info {
  display: flex;
  align-items: center;
}

.el-dropdown-link {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
}

.username {
  font-size: 14px;
  color: #606266;
}

.el-container {
  flex: 1;
  overflow: hidden;
}

.el-aside {
  background-color: #fff;
  box-shadow: 2px 0 6px rgba(0, 21, 41, 0.08);
  width: 240px !important;
  height: calc(100vh - 60px);
  overflow-y: auto;
  overflow-x: hidden;
}

.el-menu-vertical {
  height: 100%;
  border-right: none;
  padding-top: 10px;
}

:deep(.el-menu-item) {
  height: 50px;
  line-height: 50px;
  font-size: 14px;
  margin: 4px 0;
  padding: 0 20px !important;
}

:deep(.el-menu-item.is-active) {
  background-color: #ecf5ff;
  color: #409EFF;
  font-weight: 500;
}

:deep(.el-menu-item:hover) {
  background-color: #f5f7fa;
}

:deep(.el-menu-item .el-icon) {
  margin-right: 12px;
  font-size: 18px;
  width: 20px;
  text-align: center;
}

.el-main {
  background-color: #f0f2f5;
  padding: 20px;
  overflow-y: auto;
  height: calc(100vh - 60px);
}

/* 自定义滚动条样式 */
:deep(.el-aside::-webkit-scrollbar) {
  width: 6px;
}

:deep(.el-aside::-webkit-scrollbar-thumb) {
  background-color: #c0c4cc;
  border-radius: 3px;
}

:deep(.el-aside::-webkit-scrollbar-track) {
  background-color: #f5f7fa;
}
</style>