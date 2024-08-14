<template>
  <el-card id="box-card">
    <div slot="header" class="clearfix">
      <span id="title">航班查询</span>
    </div>

    <div class="box-box">
      <el-card  class="box-card2" :body-style="{ width: '100%', height: '100%' }">
        
        <el-row :gutter="20" class="row-responsive" >
          <el-col :span="9"><div class="city1">
            <label class="city-l" >{{deal.tickets[index].plane.start_city}}</label>
          </div></el-col>
          <el-col :span="6">
            <div class="from">
              <label>to</label>
            </div>
          </el-col>
          <el-col :span="9"><div class="city1">
            <label class="city-l" >{{deal.tickets[index].plane.end_city}}</label>
          </div></el-col>
        </el-row>

      </el-card>
    </div>

    <div class="block">
      <span class="demonstration">出发日期</span>
      <el-date-picker value-format="yyyy-MM-dd"
                      v-model="value1"
                      type="date"
                      placeholder="选择日期" :picker-options="pickerOptions">
      </el-date-picker>

      <el-button style="background-color: #8d9fb7 ;border-color: #8d9fb7" id="search" type="info" @click="searchHandler(deal,index)">查询</el-button>
    </div>


  </el-card>

</template>

<script>
import axios from "axios";

export default {
  name: "ChangePlane",
  data() {
    return {
      deal:this.$route.query.deal,
      index:this.$route.query.index,
      pickerOptions: {
        disabledDate(time) {
          return time.getTime() < Date.now();
        },
      },
      value1: '',
    };
  },
  methods:{
    searchHandler(deal,index) {
      sessionStorage.setItem("start_city", this.deal.tickets[this.index].plane.start_city);
      sessionStorage.setItem("end_city", this.deal.tickets[this.index].plane.end_city);
      sessionStorage.setItem("start_day", this.value1);
      this.$router.push({name: 'ChangeAnyPlane', query: {deal:deal,index:index}});

    }

  }
}


</script>

<style scoped>

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both
}

#box-card {
  width: auto;
  height: 498px;
}

#title{
  font-size: 40px;
}

.box-card2 {
  width: 580px;
  height: 150px;
  margin: auto;
}
.row-responsive {
  width: 100%; /* 设置row宽度为100%，确保子元素根据父元素宽度调整 */
}
.block{
  margin-top: 28px;
  margin-right: 100px;
}

#search{
  float: right;
  margin-left: 100px;
}

.city1{
  /*background-color: #B3C0D1;*/
  font-size: 50px;
  font-family: 'Times New Roman', Times, serif;
  text-align: center;
  width: 100%;
  height: 100%;
  
}
.city-l{
  display: inline-block;
  height: 100%;
}
.from{
  /*background-color: antiquewhite;*/
  text-align: center;
  height: 100%;
  font-size: 50px;
}

.ipt{
  padding-left: 10px;
  width: 150px;
}

</style>
