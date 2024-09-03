<template>
  <el-card id="pay">
    <label id="l_price" v-show="false">{{ this.$route.query.price }}</label>
    <label id="l_ticket_id" v-show="false">{{ this.$route.query.ticket_id }}</label>

    <el-tabs>
      <el-tab-pane label="填写付款信息">
        <el-form :label-position="labelPosition" label-width="80px" :model="info" :rules="rule" ref="info">
          <el-form-item label="姓名" prop="name">
            <el-input v-model="info.name"></el-input>
          </el-form-item>
          <el-form-item label="电话" prop="phone">
            <el-input v-model="info.phone"></el-input>
          </el-form-item>
          <el-form-item label="身份证号" prop="id">
            <el-input v-model="info.id"></el-input>
          </el-form-item>

          <el-form-item>
            <label style="font-size: 20px">￥ {{ this.$route.query.price }}&nbsp;&nbsp;&nbsp;&nbsp;</label>
            <el-button type="primary" @click="onSubmit1()" style="border-color: #8d9fb7">支付</el-button>
          </el-form-item>
        </el-form>
      </el-tab-pane>

      <el-tab-pane label="已有乘客信息">
        <el-form :label-position="labelPosition" label-width="80px">
          <el-form-item label="已有乘客:">
            <el-select v-model="selectedPassenger" placeholder="请选择已有乘客">
              <el-option
                v-for="passenger in passengers"
                :key="passenger.id"
                :label="passenger.name + ' - ' + passenger.phone_number"
                :value="passenger"
              >
              </el-option>
            </el-select>
          </el-form-item>

          <el-form-item>
            <label style="font-size: 20px">￥ {{ this.$route.query.price }}&nbsp;&nbsp;&nbsp;&nbsp;</label>
            <el-button type="primary" @click="onSubmit2()" style="border-color: #8d9fb7">支付</el-button>
          </el-form-item>
        </el-form>
      </el-tab-pane>
    </el-tabs>
  </el-card>
</template>

<script>
import axios from "axios";

export default {
  props: ['price', 'ticket_id'],
  name: "Pay",
  data() {
    return {
      p: this.$route.query,
      passengers: '',
      selectedPassenger: null,
      labelPosition: 'right',
      pass: {},
      info: {
        name: '',
        phone: '',
        id: '',
      },
      rule: {
        name: [
          { required: true, message: "请输入您的姓名", trigger: "blur" },
        ],
        phone: [
          { required: true, message: "请输入您的电话号码", trigger: "blur" },
        ],
        id: [
          { required: true, message: "请输入您的身份证号", trigger: "blur" },
        ],
      },
      radio: ''
    };
  },
  created() {
    axios.post('/getPassengers', {
      data: {
        id: encodeURI(sessionStorage.getItem("id")),
      }
    }).then((response) => {
      if (JSON.stringify(response.data) != '[]') {
        this.passengers = response.data;
      }
      console.log(this.passengers);
      console.log(response.data);
    }).catch(function (error) {
      console.log(error);
    });
  },
  methods: {
    // 将正则表达式添加到方法中
    reg_id() {
      return /^(^\d{15}$)|(^\d{17}([0-9]|X)$)/;
    },
    reg_phone() {
      return /^1[3-9]\d{9}$/;
    },
    onSubmit1() {
      this.$refs.info.validate((valid) => {
        if (valid) {
          if (!this.reg_id().test(this.info.id)) {
            this.$alert('请输入正确的身份证号码', 'error', {
              confirmButtonText: '确定'
            });
          } else {
            if (!this.reg_phone().test(this.info.phone)) {
              this.$alert('请输入正确的电话号码', 'error', {
                confirmButtonText: '确定'
              });
            } else {
              // 添加乘客信息并加入用户-乘客信息绑定表，将乘客信息绑定给该用户
              axios.post('/addPassenger', {
                data: {
                  //用户id
                  id: encodeURI(sessionStorage.getItem("id")),
                  //乘客信息
                  id_number: encodeURI(this.info.id),
                  name: encodeURI(this.info.name),
                  phone_number: encodeURI(this.info.phone),
                }
              }).then((response) => {
                console.log("正在插入顾客信息：" + response.data);
              }).catch(function (error) {
                console.log(error);
              });

              axios.post('/addDealRecord', {
                data: {
                  amount: encodeURI('-'.concat(this.p.price)),
                  id: encodeURI(sessionStorage.getItem("id")),
                  description: encodeURI("购票"),
                  time: new Date().Format("yyyy-MM-dd hh:mm:ss")
                }
              }).then((response) => {
                console.log(response.data);
              }).catch(function (error) {
                console.log(error);
              });

              // 向订单表中插入一条数据,并向deal_ticket表中插入订单和飞机票的绑定信息，并更新飞机票中的余票数
              axios.post('/pay', {
                data: {
                  price: encodeURI(this.p.price),
                  attribute: encodeURI("direct"),
                  id: encodeURI(sessionStorage.getItem("id")),
                  id_number: encodeURI(this.info.id),
                  ticket_id: encodeURI(this.p.ticket_id),
                  time: new Date().Format("yyyy-MM-dd hh:mm:ss")
                }
              }).then((response) => {
                console.log('Received form HTML:', response.data);

                // 创建一个 div 元素，将表单 HTML 插入到 div 中
                const div = document.createElement('div');
                div.id = 'paymentDiv'; // 为 div 设置一个 ID，便于调试
                div.style.display = 'none'; // 隐藏 div
                div.innerHTML = response.data;
                // 将 div 添加到文档的 body 中
                document.body.appendChild(div);
                console.log('Form appended to the body');
                // 获取表单元素并提交
                const form = div.querySelector('form');
                if (form) {
                  form.submit(); // 提交表单
                  console.log('Form submitted');
                } else {
                  console.error('Form not found in the appended HTML');
                }
              }).catch(function (error) {
                console.error('Error occurred:', error); // 输出错误信息
              });

            }
          }
        }
      });

    },
    onSubmit2() {
      if (this.selectedPassenger) {
        axios.post('/addDealRecord', {
          data: {
            amount: encodeURI('-'.concat(this.p.price)),
            id: encodeURI(sessionStorage.getItem("id")),
            description: encodeURI("购票"),
            time: new Date().Format("yyyy-MM-dd hh:mm:ss")
          }
        }).then((response) => {
          console.log(response.data);
        }).catch(function (error) {
          console.log(error);
        });

        axios.post('/pay', {
          data: {
            id_number: encodeURI(this.selectedPassenger.id_number),
            price: encodeURI(this.p.price),
            attribute: encodeURI("direct"),
            id: encodeURI(sessionStorage.getItem("id")),
            ticket_id: encodeURI(this.p.ticket_id),
            time: new Date().Format("yyyy-MM-dd hh:mm:ss")
          }
        }).then((response) => {
          console.log('Received form HTML:', response.data);
          const div = document.createElement('div');
          div.id = 'paymentDiv';
          div.style.display = 'none';
          div.innerHTML = response.data;
          document.body.appendChild(div);
          const form = div.querySelector('form');
          if (form) {
            form.submit();
          } else {
            console.error('Form not found in the appended HTML');
          }
        }).catch(function (error) {
          console.error('Error occurred:', error);
        });
      }
    }
  }
}
</script>


<style scoped>
#pay {
  -webkit-border-radius: 5px;
  border-radius: 5px;
  margin: 180px auto;
  width: 500px;
  padding: 35px 35px 15px;
  background: #fff;
  border: 1px solid #eaeaea;
  box-shadow: 0 0 25px #cac6c6;
}
</style>
