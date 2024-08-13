<template>
  <el-card id="box-card">
    <div slot="header" class="clearfix">
      <span id="title">飞机票预订</span>
    </div>

    <div class="box-box">
      <el-card class="box-card2">
        <el-form :model="cities" ref="cities">
          <el-row :gutter="20">

            <el-col :span="9">
              <div class="city2">
                <el-input :rules="rules" class="ipt" v-model="cities.input1" placeholder="请输入出发地"></el-input>
              </div>
            </el-col>

            <el-col :span="6">
              <div class="from">
                <el-button 
                  class="custom-button"
                  type="primary" icon="el-icon-refresh" @click="swapCities">
                </el-button>
              </div>
            </el-col>

            <el-col :span="9">
              <div class="city2">
                <el-input :rules="rules" class="ipt" v-model="cities.input2" placeholder="请输入目的地"></el-input>
              </div>
            </el-col>

          </el-row>
        </el-form>
      </el-card>
    </div>

    <div class="block">
      <span class="demonstration">出发日期</span>
      <el-date-picker value-format="yyyy-MM-dd"
        v-model="value1"
        type="date"
        placeholder="选择日期" :picker-options="pickerOptions">
      </el-date-picker>

      <el-button style="border-color: #8d9fb7" id="search" type="info" @click="searchHandler">查询</el-button>
    </div>


  </el-card>

</template>

<script>
import axios from "axios";

export default {
  name: "Search",
  data() {
    return {
      cities:{
        input1:"北京",
        input2:"上海"
      },
      pickerOptions: {
        disabledDate(time) {
          return time.getTime() < Date.now();
        },
      },
      value1: '',
      rules: {
        input1: [
          {
            required: true,
            message: "请输入出发地",
            trigger: "blur",
          },
        ],
        input2: [
          { required: true, message: "请输入目的地", trigger: "blur" },
        ],
      },
    };
  },
  methods:{
    searchHandler(){
      this.$refs.cities.validate((valid) => {
        if(valid){

            sessionStorage.setItem("start_city", this.cities.input1);
            sessionStorage.setItem("end_city", this.cities.input2);
            sessionStorage.setItem("start_day",this.value1);
            this.$router.push({path:'/plane'});

        }else{
          console.log('error submit!');
          return false;
        }
      })
    },
    swapCities() {
      const temp = this.cities.input1;
      this.cities.input1 = this.cities.input2;
      this.cities.input2 = temp;
    },

  }
}


</script>

<style scoped>
.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both
}

#box-card {
  width: 800px;
  height: 540px;
  margin: auto;
}

#title{
  font-size: 40px;
}

.box-card2 {
  width: 580px;
  height: 100px;
  margin: auto;
}
.custom-button {
  border-radius: 12px; /* 圆角 */
  padding: 15px 30px; /* 增大尺寸 */
  background-color: #ffffff; /* 平时是浅白色 */
  color: #333; /* 文字颜色 */
  
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2); /* 阴影效果 */
  transition: background-color 0.3s, box-shadow 0.3s; /* 悬浮效果的过渡 */
}

.custom-button:hover {
  background-color: #ededed; /* 悬浮是浅蓝色 */
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.3); /* 悬浮时的阴影效果 */
}

.block{
  margin-top: 30px;
  margin-right: 100px;
}

#search{
  float: right;
  margin-left: 100px;
}

.city1{
  /*background-color: #B3C0D1;*/
  font-size: 50px;
  text-align: center;
  width: 200px;
  height: 120px;
  padding-top: 40%;
}
.city-l{
  display: inline-block;
  height: 100px;
}


.ipt{
  padding-left: 10px;
  width: 150px;
}

</style>
