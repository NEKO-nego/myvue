<template>
  <el-card style="width: 100%; height: 100vh; padding: 20px; margin: auto;">
    <div style="display: flex; height: 100%;">

      <!-- 长条滑动边栏 -->
      <transition name="slide">
        <div v-if="isSidebarVisible" class="bb left">
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
                  <span>我的订单</span>
                </template>
                <el-menu-item-group>
                  <el-menu-item index="/dealPaid">已付款</el-menu-item>
                  <el-menu-item index="/dealUnpaid">已退款</el-menu-item>
                </el-menu-item-group>
              </el-submenu>

              <el-menu-item index="/passengerInfo">
                <i class="el-icon-menu"></i>
                <span slot="title">乘客信息</span>
              </el-menu-item>

              <el-menu-item index="/personalNotice">
                <i class="el-icon-document"></i>
                <span slot="title">通知信息</span>
              </el-menu-item>

              <el-menu-item index="/record">
                <i class="el-icon-menu"></i>
                <span slot="title">交易记录</span>
              </el-menu-item>
            </el-menu>
          </div>
        </div>
      </transition>

      <!-- 主内容区 -->
      <el-col :span="isSidebarVisible ? 18 : 24" class="bb right">
        <router-view></router-view>
      </el-col>
      
    </div>
  </el-card>
</template>

<script>
export default {
  name: "Personal",
  data(){
    return{
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
.el-card {
  overflow: auto;
}

.slide-enter-active, .slide-leave-active {
  transition: all 0.3s ease;
}

.slide-enter, .slide-leave-to {
  transform: translateX(-100%);
}

.bb {
  height: 100%;
}

.left {
  
  border-radius: 5%;
  width: 250px;

  background-color: #edfbfb; /* 浅色背景 */
  padding: 20px;
  height: 100vh; /* 铺满整个左侧 */
  position: relative;
  border-right: 1px solid #dcdfe6;

}

.right {
  background-color: #ffffff;
  padding: 20px;
  flex-grow: 1;
}

#user {
  height: 60px;
  width: 60px;
  background-image: url("https://static.thenounproject.com/png/5105250-200.png");
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

.sidebar-content {
  padding-top: 20px;
}
</style>
