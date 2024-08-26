<template>
  <el-card style="width: 100%; height: 100vh; padding: 20; margin: auto;">
    <div style="display: flex; height: 100%;">

      <!-- 长条滑动边栏 -->
      <transition name="slide">
        <div v-if="isSidebarVisible" class="sidebar">
          <div class="sidebar-content">
            <el-row class="user-section">
              <el-col :span="8">
                <div id="user"></div>
              </el-col>
              <el-col :span="16">
                <div class="username">{{ username }}</div>
              </el-col>
            </el-row>

            <el-menu
              :router="true"
              default-active="2"
              class="el-menu-vertical-demo"
              @open="handleOpen"
              @close="handleClose">
              <el-submenu index="1">
                <template slot="title">
                  <i class="el-icon-location"></i>
                  <span>管理航班</span>
                </template>
                <el-menu-item-group>
                  <el-menu-item index="/addPlane">添加航班</el-menu-item>
                  <el-menu-item index="/deletePlane">取消航班</el-menu-item>
                </el-menu-item-group>
              </el-submenu>

              <el-menu-item index="dealSearch">
                <i class="el-icon-document"></i>
                <span slot="title">订单查询</span>
              </el-menu-item>
            </el-menu>
          </div>
        </div>
      </transition>

      <!-- 主内容区 -->
       
      <el-col :span="isSidebarVisible ? 24 : 30" class="main-content">       
        <router-view></router-view>
      </el-col>
      
    </div>
  </el-card>
  
</template>

<script>
export default {
  name: "Root",
  data() {
    return {
      username: sessionStorage.getItem("username"),
      isSidebarVisible: true,
    }
  },
  methods: {
    handleOpen(key, keyPath) {
      console.log(key, keyPath);
    },
    handleClose(key, keyPath) {
      console.log(key, keyPath);
    },
    toggleSidebar() {
      this.isSidebarVisible = !this.isSidebarVisible;
    }
  }
}
</script>

<style scoped>
.el-card{
  overflow: auto;
}
.slide-enter-active, .slide-leave-active {
  transition: all 0.3s ease;
}
.slide-enter, .slide-leave-to /* .slide-leave-active in <2.1.8 */ {
  transform: translateX(-100%);
}

.sidebar {
  border-radius: 5%;
  width: 250px;
  background-color: #ecfae4; /* 浅色背景 */
  padding: 20px;
  height: 100vh; /* 铺满整个左侧 */
  position: relative;
  border-right: 1px solid #dcdfe6;
}

.sidebar-content {
  padding-top: 20px;
}

.user-section {
  margin-bottom: 20px;
}

#user {
  height: 60px;
  width: 60px;
  background-image: url("../image/man.png");
  background-size: cover;
  border-radius: 50%;
}

.username {
  color: #606266; /* 深灰色字体 */
  font-size: 16px;
  padding-top: 20px;
}

.el-menu-vertical-demo {
  background-color: transparent;
  color: #606266; /* 调整文字颜色 */
}

.el-menu-item, .el-submenu__title {
  color: #606266;
}

.el-menu-item:hover, .el-submenu__title:hover {
  background-color: #e4e7ed;
}

.main-content {
  background-color: #ffffff;
  height: 100%;
  padding: 20px;
}

.toggle-btn {
  position: relative;
  top: 20px;
  left: 20px;
  z-index: 10000;
  background-color: #e6edf7; /* 浅色背景 */
  color: #606266; /* 图标颜色 */
  border: none;
  border-radius: 50%; /* 圆形按钮 */
  padding: 15px; /* 调整按钮大小 */
  transition: background-color 0.3s, transform 0.3s; /* 动态效果 */
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1); /* 添加阴影 */
}

.toggle-btn:hover {
  background-color: #d0d9e8; /* 悬浮时背景颜色 */
  transform: scale(1.1); /* 悬浮时放大效果 */
  cursor: pointer; /* 鼠标悬停时变成指针 */
}

.toggle-btn i {
  font-size: 40px; /* 图标大小，覆盖整个按钮 */
}


.toggle-btn:hover {
  background-color: #dcdfe6;
}
</style>
