<template>
  <!-- <div :style="{backgroundImage:'url('+backImg+')' }"> -->
    <div>
    <el-form class="login-container" label-position="left"
           label-width="0px" :style="{backgroundImage:'url('+backImg+')' }" >
    <h3 class="login_title">TJU考勤系统登录</h3>
    <el-form-item prop="account">
      <el-input type="text" v-model="loginForm.username" auto-complete="off" placeholder="账号"></el-input>
    </el-form-item>
    <el-form-item prop="checkPass">
      <el-input type="password" v-model="loginForm.password" auto-complete="off" placeholder="密码"></el-input>
    </el-form-item>
    <el-checkbox class="login_remember" v-model="checked" label-position="left">记住密码</el-checkbox>
    <el-form-item style="width: 100%">
      <el-button type="success" @click.native.prevent="submitClick" style="width: 100%">登录</el-button>
    </el-form-item>
  </el-form>
  </div>
</template>
<script>
  export default{
    data(){
      return {
       loginForm:{
         username:'',
         password:''
       },
       backImg: require('../assets/img1.jpeg'),
       checked: true,
      }
    },
    methods: {
      submitClick: function () {
        var _this = this;
        if (_this.loginForm.username == ''||_this.loginForm.password == '') {
          this.$alert('账号或密码不能为空', '提示', {
            confirmButtonText: '确定',
          });
        }else{
          _this.$http.get('/login')
          _this.$http.get('/login/'+_this.loginForm.username+'/'+_this.loginForm.password).then(function (response) {
            console.log(response);
            if (response.data.code == 200) {
              _this.$alert('登录成功', '提示', {
                confirmButtonText: '确定',
              });
              console.log(response.data.data.empId);
              window.sessionStorage.setItem('empId',response.data.data.empId);
              window.sessionStorage.setItem('account',response.data.data.account);
              window.sessionStorage.setItem('name',response.data.data.name);
              window.sessionStorage.setItem('role',response.data.data.role); 
              _this.$router.replace({path: '/DaKa/EmployeDaKa'});   
            }else if(response.data.code == 305){
              _this.$alert(response.data.message, '提示', {
                confirmButtonText: '确定',
              });
            }else{
              _this.$alert(response.data.message, '提示', {
                confirmButtonText: '确定',
              });
            }
          });
        }
      }
    }
  }
</script>
<style>
  .login-container {
    border-radius: 15px;
    background-clip: padding-box;
    margin: 180px auto;
    width: 350px;
    padding: 35px 35px 15px 35px;
    background: #fff;
    border: 1px solid #eaeaea;
    box-shadow: 0 0 25px #cac6c6;
  }

  .login_title {
    margin: 0px auto 40px auto;
    text-align: center;
    color: #505458;
  }

  .login_remember {
    margin: 0px 0px 35px 0px;
    text-align: center;
  }
</style>
