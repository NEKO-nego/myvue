<template>
  <div id="addPlane">
    <div style="text-align: center;margin-top: 15px;margin-bottom: 15px"><h3>航班信息</h3></div>
    <el-form :label-position="labelPosition" label-width="80px" :model="info">
      <el-row>
        <el-col :span="2">&nbsp</el-col>
        <el-col :span="9">
          <el-form-item label="出发城市">
            <el-autocomplete
              v-model="info.start_city"
              :fetch-suggestions="querySearch"
              placeholder="请输入出发城市"
            ></el-autocomplete>
          </el-form-item>
          <el-form-item label="到达城市">
            <el-autocomplete
              v-model="info.end_city"
              :fetch-suggestions="querySearch"
              placeholder="请输入到达城市"
            ></el-autocomplete>
          </el-form-item>
          <el-form-item label="出发日期">
            <el-date-picker value-format="yyyy-MM-dd" type="date" placeholder="选择日期" v-model="info.start_day" style="width: 100%;"></el-date-picker>
          </el-form-item>
          <el-form-item label="到达日期">
            <el-date-picker value-format="yyyy-MM-dd" type="date" placeholder="选择日期" v-model="info.end_day" style="width: 100%;"></el-date-picker>
          </el-form-item>
          <el-form-item label="班次价格">
            <el-input v-model="info.price"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="2">&nbsp</el-col>
        <el-col :span="9">
          <el-form-item label="出发机场">
            <el-input v-model="info.departure_airfield"></el-input>
          </el-form-item>
          <el-form-item label="到达机场">
            <el-input v-model="info.arrival_airfield"></el-input>
          </el-form-item>
          <el-form-item label="出发时间">
            <el-time-picker value-format="HH:mm:ss" placeholder="选择时间" v-model="info.start_time" style="width: 100%;"></el-time-picker>
          </el-form-item>
          <el-form-item label="到达时间">
            <el-time-picker value-format="HH:mm:ss" placeholder="选择时间" v-model="info.end_time" style="width: 100%;"></el-time-picker>
          </el-form-item>
        </el-col>
      </el-row>

      <div v-for="(ticket, index) in info.tickets" :key="index">
        <ticket :index="index" :tickets="info.tickets"></ticket>
      </div>

      <div style="margin: 20px;width: auto;height: 1px;background-color: #66ccff "></div>

      <el-form-item>
        <el-button type="primary" @click="onSubmit" style=" border-color: #8d9fb7">添加</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>


<script>
import axios from "axios";

const reg_ticket_number = /^([1-9]|[1-9][0-9]|100)$/;
const reg_ticket_count = /^([1-9]|[1-9][0-9]|[1][0-9][0-9]|200)$/;
const reg_charater = /[\u4e00-\u9fa5]{1,10}/;
const reg_price = /^([1-9]|[1-9][0-9]|[1-9][0-9][0-9]|[1-9][0-9][0-9][0-9]|10000)$/;

export default {
  name: "addPlane",
  data() {
    return {
      labelPosition: "right",
      info: {
        end_city: "",
        start_city: "",
        start_day: "",
        end_day: "",
        start_time: "",
        end_time: "",
        price: "",
        departure_airfield: "",
        arrival_airfield: "",
        tickets: [{ coun: "", number_all: "" }],
      },
      cityOptions: [],
    };
  },
  components: {
    ticket: {
      props: ["index", "tickets"],
      template:
        '<el-row> ' +
        '<el-col :span="2">&nbsp</el-col> ' +
        '<el-col :span="9">' +
        '<el-form-item label="折扣">' +
        '<el-input v-model="tickets[index].coun"></el-input>' +
        "</el-form-item>  " +
        "</el-col> " +
        '<el-col :span="2">&nbsp</el-col> ' +
        '<el-col :span="9">' +
        '<el-form-item label="票数">' +
        '<el-input v-model="tickets[index].number_all"></el-input>' +
        "</el-form-item>  " +
        "</el-col>" +
        "</el-row>",
    },
  },
  methods: {
    async getAllCities() {
      try {
        const response = await axios.post("/getAllCities");
        this.cityOptions = response.data;
        console.log(this.cityOptions)
      } catch (error) {
        console.error("Failed to fetch cities:", error);
      }
    },
    querySearch(queryString, cb) {
      console.log('Query:', queryString);
      const results = queryString
        ? this.cityOptions.filter((city) =>
        city.toLowerCase().includes(queryString.toLowerCase())
      )
      : this.cityOptions;
      const formattedResults = results.map(city => ({ value: city }));

      console.log('Formatted Results:', formattedResults);
      cb(formattedResults);
    },


    onSubmit() {
      var sub = true;
      if (!reg_charater.test(this.info.start_city)) {
        this.$alert("请输入正确的出发城市", "error", {
          confirmButtonText: "确定",
        });
      } else if (!reg_charater.test(this.info.end_city)) {
        this.$alert("请输入正确的终点城市", "error", {
          confirmButtonText: "确定",
        });
      } else if (!reg_charater.test(this.info.departure_airfield)) {
        this.$alert("请输入正确的出发机场", "error", {
          confirmButtonText: "确定",
        });
      } else if (!reg_charater.test(this.info.arrival_airfield)) {
        this.$alert("请输入正确的到达机场", "error", {
          confirmButtonText: "确定",
        });
      } else if (!reg_price.test(this.info.price)) {
        this.$alert("请设置0-10000的机票价格", "error", {
          confirmButtonText: "确定",
        });
      } else {
        this.info.tickets.forEach((ticket, index) => {
          if (!reg_ticket_count.test(ticket.coun)) {
            this.$alert("请设置大于0-200的机票折扣", "error", {
              confirmButtonText: "确定",
            });
            sub = false;
          } else if (!reg_ticket_number.test(ticket.number_all)) {
            this.$alert("请设置0-100的机票数目", "error", {
              confirmButtonText: "确定",
            });
            sub = false;
          }
        });
        if (sub) {
          axios
            .post("/addPlane", {
              data: {
                end_city: encodeURI(this.info.end_city),
                start_city: encodeURI(this.info.start_city),
                start_day: encodeURI(this.info.start_day),
                end_day: encodeURI(this.info.end_day),
                start_time: encodeURI(this.info.start_time),
                end_time: encodeURI(this.info.end_time),
                price: encodeURI(this.info.price),
                departure_airfield: encodeURI(this.info.departure_airfield),
                arrival_airfield: encodeURI(this.info.arrival_airfield),
                tickets: this.info.tickets,
              },
            })
            .then((response) => {
              if (response.data) {
                this.$alert("添加成功", "信息", {
                  confirmButtonText: "确定",
                });
                this.$router.push("/root");
              }
            })
            .catch(function (error) {
              console.log(error);
            });
        }
      }
      console.log(this.info);
    },
  },
  mounted() {
    this.getAllCities(); // Fetch all cities on component mount
  },
};
</script>


<style scoped>
@import "../assets/css/button.css";
#addPlane {
  width: auto;
  height: 498px;
  overflow: auto;
}

/* 添加到 <style scoped> */
.el-form-item {
  margin-bottom: 16px; /* 调整表单项的底部间距 */
}

.el-date-picker {
  width: 100%; /* 确保日期选择器宽度100% */
}

</style>

