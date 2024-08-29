<template>
  <el-tabs id="box" type="border-card">
    <el-tab-pane>
      <el-button id="change" @click="change" type="text">北京时间</el-button>
      <span slot="label"><label>{{ start_day }}</label></span>
      <el-select v-model="sortOption" placeholder="排序方式" @change="sortPlanes">
        <el-option label="总价格" value="price"></el-option>
        <el-option label="总耗时" value="time"></el-option>
      </el-select>
      <AllPlanes
        ref="allP"
        v-if="planes[0].start_day != null"
        v-for="(plane, index) in planes"
        :message="plane"
        :key="plane.plane_id"
        :buy="true"
      ></AllPlanes>
      <div v-if="planes[0].start_day == null">
        <span>当日无航班</span>
      </div>
    </el-tab-pane>
    <el-tab-pane>
      <span slot="label">中转</span>
      <el-select v-model="sortTransitOption" placeholder="排序方式" @change="sortTransitPlanes">
        <el-option label="总价格" value="price"></el-option>
        <el-option label="总耗时" value="time"></el-option>
      </el-select>
      <TransitPlanes
        v-if="transit[0].planes1.start_day != null"
        v-for="plane in transit"
        :message="plane"
        :key="plane.planes1.plane_id"
      ></TransitPlanes>
      <div v-if="transit[0].planes1.start_day == null">
        <span>当日无航班</span>
      </div>
    </el-tab-pane>
  </el-tabs>
</template>

<script>
import AllPlanes from "./AllPlanes";
import TransitPlanes from "./TransitPlanes";
import axios from "axios";

export default {
  name: "Plane",
  components: { AllPlanes, TransitPlanes },
  data() {
    return {
      planes: [{}],
      transit: [
        {
          planes1: {},
          planes2: {},
        },
      ],
      add_time: "",
      start_day: sessionStorage.getItem("start_day"),
      sortOption: "", // for direct flights
      sortTransitOption: "", // for transit flights
    };
  },
  mounted() {
    axios
      .post("/search2", {
        data: {
          start_city: encodeURI(sessionStorage.getItem("start_city")),
          end_city: encodeURI(sessionStorage.getItem("end_city")),
          start_day: sessionStorage.getItem("start_day"),
        },
      })
      .then((response) => {
        if (JSON.stringify(response.data) != "[]") {
          this.planes = response.data;
        }
      })
      .catch(function (error) {
        console.log(error);
      });
    axios
      .post("/transit", {
        data: {
          start_city: encodeURI(sessionStorage.getItem("start_city")),
          end_city: encodeURI(sessionStorage.getItem("end_city")),
          start_day: sessionStorage.getItem("start_day"),
        },
      })
      .then((response) => {
        if (JSON.stringify(response.data) != "[]") {
          this.transit = response.data;
        }
        console.log(response.data);
      })
      .catch(function (error) {
        console.log(error);
      });
    axios
      .post("/change_time", {
        data: {
          city: encodeURI(sessionStorage.getItem("end_city")),
        },
      })
      .then((response) => {
        this.add_time = response.data;
      })
      .catch(function (error) {
        console.log(error);
      });
  },
  methods: {
  // 解析时间和日期字符串为 Date 对象
  parseDateTime(date, time) {
    let [year, month, day] = date.split('-').map(Number);
    let [hours, minutes, seconds] = time.split(':').map(Number);
    return new Date(year, month - 1, day, hours, minutes, seconds);
  },

  // 计算两个时间点之间的时间差（分钟）
  getTimeDifferenceInMinutes(date1, time1, date2, time2) {
    let datetime1 = this.parseDateTime(date1, time1);
    let datetime2 = this.parseDateTime(date2, time2);
    let differenceInMilliseconds = datetime2 - datetime1; // 时间差（毫秒）
    console.log("minus: "+differenceInMilliseconds / (1000 * 60))
    return differenceInMilliseconds / (1000 * 60); // 转换为分钟
  },

  calculateTimeDifference_2(a) {
    let timeDifference = this.getTimeDifferenceInMinutes(a.start_day, a.start_time, a.end_day, a.end_time);    
    return timeDifference ;
  },
  calculateTimeDifference_1(a) {
    let timeDifference = this.getTimeDifferenceInMinutes(a.planes1.start_day, a.planes1.start_time, a.planes2.end_day, a.planes2.end_time);
    // 示例条件：检查时间差是否大于 0
    return timeDifference;
  },

    //用于用时排序的算法
    change() {
      var bb = document.getElementById("change").innerText;
      var head = this.add_time.substring(0, 1);
      if (bb === "北京时间") {
        this.planes.forEach((plane, index) => {
          this.$refs.allP[index].change_time(this.add_time);
        });

        document.getElementById("change").innerText = "当地时间";
        if (head === "+") {
          this.add_time = this.add_time.replace(/[+]/, "-");
        } else if (head === "-") {
          this.add_time = this.add_time.replace(/-/, "+");
        }
      } else if (bb === "当地时间") {
        this.planes.forEach((plane, index) => {
          this.$refs.allP[index].change_time(this.add_time);
        });
        document.getElementById("change").innerText = "北京时间";

        if (head === "+") {
          this.add_time = this.add_time.replace(/[+]/, "-");
        } else if (head === "-") {
          this.add_time = this.add_time.replace(/-/, "+");
        }
      }
    },
    sortPlanes() {
      if (this.sortOption === "price") {
        this.planes.sort((a, b) => a.lowest_price - b.lowest_price);
        console.log("全部数据:", this.planes);

      } else if (this.sortOption === "time") {
        this.planes.sort((a, b) => this.calculateTimeDifference_2(a)-this.calculateTimeDifference_2(b));
      }
    },
    sortTransitPlanes() {
      if (this.sortTransitOption === "price") {
        console.log("now is price_sort");
        this.transit.sort(
          (a, b) => (a.planes1.lowest_price + a.planes2.lowest_price) - (b.planes1.lowest_price + b.planes2.lowest_price)
        );
      } else if (this.sortTransitOption === "time") {
        console.log("now is time_sort");
        this.transit.sort(
          (a, b) =>
            this.calculateTimeDifference_1(a)-this.calculateTimeDifference_1(b)
        );
      }
    },
  },
};
</script>

<style scoped>
#box {
  margin: auto;
  width: 1000px;
  min-height: 540px;
}
</style>
