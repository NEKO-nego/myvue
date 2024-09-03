<template>
  <div class="login-container">
    <el-form
      :model="ruleForm2"
      :rules="rules2"
      status-icon
      ref="ruleForm2"
      label-position="left"
      label-width="0px"
      class="demo-ruleForm login-page"
    >
      <h3 class="title" style="text-align: center">机票售卖系统</h3>
      <el-form-item prop="username">
        <el-input
          type="text"
          v-model="ruleForm2.username"
          auto-complete="off"
          placeholder="用户名"
        ></el-input>
      </el-form-item>
      <el-form-item prop="password">
        <el-input
          type="password"
          v-model="ruleForm2.password"
          auto-complete="off"
          placeholder="密码"
        ></el-input>
      </el-form-item>
      <el-form-item style="width: 100%">
        <el-button
          type="info"
          style="width: 100%  ;border-color: #8d9fb7"
          @click="handleSubmit"
          :loading="logining"
        >登录</el-button>
      </el-form-item>
      <el-form-item style="width: 100%">
        <el-button
          type="success"
          style="width: 100%; margin-top: 10px"
          @click="handleRegister"
        >注册</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "login",
  data() {
    return {
      suc: "",
      logining: false,
      ruleForm2: {
        id: 0,
        username: "",
        password: "",
      },
      rules2: {
        username: [
          { required: true, message: "请输入您的账号", trigger: "blur" },
          { min: 3, max: 20, message: "用户名长度应在3到20个字符之间", trigger: "blur" },
          { pattern: /^[a-zA-Z0-9_]+$/, message: "用户名只能包含字母、数字和下划线", trigger: "blur" }
        ],
        password: [
          { required: true, message: "请输入您的密码", trigger: "blur" },
          { min: 6, max: 20, message: "密码长度应在6到20个字符之间", trigger: "blur" },
          { pattern: /^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{6,}$/, message: "密码必须包含字母和数字", trigger: "blur" }
        ],
      },
    };
  },
  methods: {
    handleSubmit() {
      this.$refs.ruleForm2.validate((valid) => {
        if (valid) {
          this.logining = true;
          axios
            .post("/login", {
              data: {
                name: encodeURI(this.ruleForm2.username),
                pwd: encodeURI(this.ruleForm2.password),
              },
            })
            .then((response) => {
              this.suc = response.data;
              console.log(response);

              if (this.suc.id != null && this.suc.id == 90000000) {
                this.logining = false;
                sessionStorage.setItem("id", this.suc.id);
                sessionStorage.setItem("username", this.ruleForm2.username);
                sessionStorage.setItem("root", "1");
                this.$alert("登录成功", "登录成功", {
                  confirmButtonText: "ok",
                });
                this.$router.push({ path: "/root" });
              } else if (this.suc.id != null) {
                this.logining = false;
                sessionStorage.setItem("id", this.suc.id);
                sessionStorage.setItem("username", this.ruleForm2.username);
                sessionStorage.setItem("root", "0");
                this.$alert("登录成功", "登录成功", {
                  confirmButtonText: "ok",
                });
                this.$router.push({ path: "/personal" });
              } else {
                this.logining = false;
                this.$alert("用户名或密码错误", "登录失败", {
                  confirmButtonText: "ok",
                });
              }
            })
            .catch(function (error) {
              console.log(error);
            });
        } else {
          console.log("error submit!");
          return false;
        }
      });
    },
    handleRegister() {
      this.$refs.ruleForm2.validate((valid) => {
        if (valid) {
          axios
            .post("/addUser", {
              data: {
                name: encodeURI(this.ruleForm2.username),
                pwd: encodeURI(this.ruleForm2.password),
              },
            })
            .then((response) => {
              console.log(response);
              if (response.data == 1) {
                this.$alert("注册成功", "注册成功", {
                  confirmButtonText: "ok",
                });
                this.$router.push({ path: "/login" });
              } else {
                this.$alert("注册失败", response.data.message || "注册失败", {
                  confirmButtonText: "ok",
                });
              }
            })
            .catch((error) => {
              console.log(error);
              this.$alert("出现错误注册失败", "注册失败", {
                confirmButtonText: "ok",
              });
            });
        } else {
          console.log("error submit!");
          return false;
        }
      });
    },
  },
};
</script>

<style scoped>
@import '../assets/css/main.css';
.login-container {
  width: 100%;
  height: 100%;
}
.login-page {
  border-radius: 5px;
  margin: 180px auto;
  width: 350px;
  padding: 35px 35px 15px;
  background: #fff;
  border: 1px solid #eaeaea;
  box-shadow: 0 0 25px #cac6c6;
}
h3 {
  text-align: left;
}
</style>
