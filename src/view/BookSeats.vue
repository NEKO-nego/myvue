<template>
  <div class="container">
    <div class="content">
      <h1>座位选择</h1>
      <p>订单号: {{ deal_id }}</p>

      <!-- Input for seat number -->
      <div class="input-group">
        <label for="seatNumber">选择座位号 (0-99): </label>
        <input
          id="seatNumber"
          type="number"
          v-model="seatNumber"
          min="0"
          max="99"
          required
        />
      </div>
      
      <!-- Submit button -->
      <button @click="submitSeatBooking" class="btn">提交</button>

      <!-- Button to get seat availability -->
      <button @click="getSeatsAvailability" class="btn">获取座位情况</button>

      <!-- Display seat availability in a grid -->
      <div v-if="seatsArray.length" class="plane-layout">
        <div class="seat-section">
          <div
            v-for="(seat, index) in seatsArray.slice(0, 40)"
            :key="index"
            :class="['seat', seat ? 'occupied' : 'available']"
            @click="selectSeat(index)"
          >
            {{ index }}
          </div>
        </div>
        <div class="seat-section">
          <div
            v-for="(seat, index) in seatsArray.slice(40)"
            :key="index + 40"
            :class="['seat', seat ? 'occupied' : 'available']"
            @click="selectSeat(index + 40)"
          >
            {{ index + 40 }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'BookSeats',
  data() {
    return {
      deal_id: '', // 初始化为空字符串
      seatNumber: '', // 用户输入的座位号
      seatsArray: [] // 存储座位情况的布尔数组
    };
  },
  mounted() {
    // 确保 deal_id 被正确读取
    this.deal_id = this.$route.query.deal.deal_id;
    console.log('Deal ID:', this.deal_id); // 打印 deal_id

    if (!this.deal_id) {
      this.$alert('未找到有效的订单号', '错误', {
        confirmButtonText: '确定'
      });
    }
  },
  methods: {
    submitSeatBooking() {
      // 验证座位号是否有效
      if (this.seatNumber === '' || this.seatNumber < 0 || this.seatNumber > 99) {
        this.$alert('请输入有效的座位号（0-99）', '警告', {
          confirmButtonText: '确定'
        });
        return;
      }

      // 发送座位预定请求
      axios.post('/updateSeatAndPlane', {
        data: {
          deal_id: `${this.deal_id}`,
          seat: this.seatNumber
        }
      })
      .then((response) => {
        if (response.data) {
          this.$alert('座位预定成功', '信息', {
            confirmButtonText: '确定'
          });
          this.$router.push({ path: '/refresh' });
        } else {
          this.$alert('座位预定失败', '信息', {
            confirmButtonText: '确定'
          });
        }
      })
      .catch((error) => {
        console.log(error);
        this.$alert('发生错误，请重试', '错误', {
          confirmButtonText: '确定'
        });
      });
    },
    getSeatsAvailability() {
      axios.post('/getSeatsArray', {
        planeId: this.deal_id // 使用deal_id作为planeId进行请求
      })
      .then((response) => {
        this.seatsArray = response.data; // 保存返回的布尔数组
        console.log("排座情况：" + this.seatsArray);
      })
      .catch((error) => {
        console.log(error);
        this.$alert('获取座位情况失败，请重试', '错误', {
          confirmButtonText: '确定'
        });
      });
    },
    selectSeat(index) {
      this.seatNumber = index;
      this.$alert(`您已选择座位号: ${index}`, '信息', {
        confirmButtonText: '确定'
      });
    }
  }
}
</script>

<style scoped>
/* 全局容器样式 */
.container {
  display: flex;
  justify-content: center; /* 水平居中 */
  align-items: center; /* 垂直居中 */
  height: 100vh; /* 使页面内容垂直居中 */
}

/* 内容容器样式 */
.content {
  text-align: center;
}

/* 输入框和按钮的样式 */
.input-group {
  margin-bottom: 20px;
}

.btn {
  margin: 5px;
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
}

/* 飞机布局容器 */
.plane-layout {
  display: flex;
  flex-direction: column;
  align-items: center;
}

/* 座位区域 */
.seat-section {
  display: grid;
  grid-template-columns: repeat(20, 1fr); /* 20列的网格 */
  gap: 10px;
  margin-bottom: 20px; /* 每个区域之间的间隔 */
  max-width: 1000px; /* 限制网格最大宽度 */
}

/* 单个座位格子的样式 */
.seat {
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  border-radius: 5px;
  font-weight: bold;
  text-align: center;
}

/* 已占用的座位样式 */
.occupied {
  background-color: red;
  color: white;
}

/* 可用的座位样式 */
.available {
  background-color: green;
  color: white;
}
</style>
