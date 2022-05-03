<template>
  <el-container >
  <el-header style="font-size: 16px;background-color:#0d5056;border-radius: 8px;height:80px  " >
    <el-row type="flex" justify="space-between">
      <el-col :span="10" style="font-size: 24px;color: #ffffff">TJU考勤管理系统</el-col>
      <el-col :span="6"></el-col>
      <el-col :span="2">
        <el-dropdown @command="handleCommand">
          <el-button style="border-radius: ; " >
            角色 - {{formatRole(role)}}<i class="el-icon-arrow-down el-icon--right"></i>
          </el-button>
          <el-dropdown-menu slot="dropdown">
        <el-dropdown-item command="myInfo">个人中心</el-dropdown-item>    
        <el-dropdown-item command="logout">退出登录</el-dropdown-item>
      </el-dropdown-menu>
        </el-dropdown>
      </el-col>
    </el-row>
    
    </el-header>

  <el-container style="height: 100vh; border-radius:12px;background-color:#f0f2f5;">
    <el-aside width="300px" style="border: 3px solid #ffffff;background-color:#0d5056;border-radius:12px;">
    <div class="personal_position">
      <img
        src="../assets/pdx.png"
        alt="个人的头像"
        class="personal_logo">
    </div>
    <div class="personal_name">
        <p>{{name}} - {{formatRole(role)}}</p>
    </div>  
    <div class="personal_state">
        <p>当前状态 - {{state}}</p>
    </div> 
    <div style="margin-bottom : 10px">
      <el-button type="success" style="margin-left : 50px" @click="changeState1()" >{{checkIn}}</el-button>
      <el-button type="warning" style="margin-left : 10px" @click="changeState2()" :disabled='!isHere'>{{checkOut}}</el-button>
    </div>

    <el-menu 
      router 
      :default-openeds="[activeIndex]"
      unique-opened
      background-color="#0d5056"
      text-color="#fff"
      active-text-color="#fff"
      @open="handleOpen"
      >
        <el-submenu index="3">
          <template slot="title">个人中心</template>
          <el-menu-item index="/employer/MyInfo">我的信息</el-menu-item>
        </el-submenu>
        <el-submenu index="2">
          <template slot="title">打卡中心</template>
          <el-menu-item index="/DaKa/EmployeDaKa">查看打卡记录</el-menu-item>
        </el-submenu>
        <el-submenu index="5">
          <template slot="title">申请假期</template>
          <el-menu-item index="/apply/addApply">添加申请</el-menu-item>
          <el-menu-item index="/apply/applyList">请假记录</el-menu-item>
        </el-submenu>
        <el-submenu index="1">
          <template slot="title">审批假期</template>
          <el-menu-item index="/apply/ManageApply" :disabled="ismanager">部门经理审核申请</el-menu-item>
          <el-menu-item index="/apply/ManageApplyX" :disabled="ismanager">总经理审核申请</el-menu-item>
        </el-submenu>
        <el-submenu index="4">
          <template slot="title">人事管理</template>
          <el-menu-item index="/employer/AddEmployer" :disabled="isrenshi">添加员工</el-menu-item>
          <el-menu-item index="/employer/AllEmployeState" :disabled="isrenshi">员工状态</el-menu-item>
          <el-menu-item index="/employer/EmployerInfo" :disabled="isrenshi">员工详情</el-menu-item>
          <el-menu-item index="/employer/AllEmployer" :disabled="isrenshi">查询所有人员</el-menu-item>
        </el-submenu>
        <el-submenu index="6">
          <template slot="title">财务管理</template>
          <el-menu-item index="/DaKa/AllDaKa" :disabled="iscaiwu">所有打卡记录</el-menu-item>
          <el-menu-item index="/apply/AllApplyList" :disabled="iscaiwu">所有请假申请</el-menu-item>
        </el-submenu>
    </el-menu>
  </el-aside>
  
    
    <el-main>
      <router-view></router-view>
    </el-main>
  </el-container>
</el-container>
</template>

<style>
body,html{
  padding: 0;
  margin: 0;
}
  .el-header {
    background-color: #B3C0D1;
    color: #333;
    line-height: 60px;
  }
  
  .el-aside {
    color: #333;
  }
</style>

<script>
  export default {
    data() {
      return {
        name : '',
        ismanager : false,
        isrenshi : false,
        iscaiwu: false,
        isCollapse: false,
        activeIndex: '3',
        role : '',
        state : '在班',
        checkIn : '下班打卡',
        checkOut : '外出打卡',
        stateNum : '',
        isHere: false,
      }
    },
    created() {
      if (window.sessionStorage.getItem('empId') == null) {
        this.$router.replace({path: '/login'});
      }else {
        this.name = window.sessionStorage.getItem('name');
        this.role = window.sessionStorage.getItem('role');
        this.loadstate();
      }
      
      if (window.sessionStorage.getItem('role') == 1 || window.sessionStorage.getItem('role') == 2 ||window.sessionStorage.getItem('role') == 3  ) {
        this.ismanager = false;
        this.isrenshi = false;
        this.iscaiwu = false;
      }else if (window.sessionStorage.getItem('role') == 4 ) {
        this.isrenshi = false;
        this.ismanager = true;
        this.iscaiwu = true;
      }else if (window.sessionStorage.getItem('role') == 5) {
        this.ismanager = true;
        this.isrenshi = true;
        this.iscaiwu = false;
      }else {
        this.ismanager = true;
        this.isrenshi = true;
        this.iscaiwu = true;
      }
    },
    methods:{
      handleCommand(command) {
        if (command === 'logout') {
          window.sessionStorage.removeItem('empId');
          window.sessionStorage.removeItem('name');
          this.$router.replace({path: '/login'});
        }
        if (command === 'myInfo') {
          this.$router.replace({path: '/employer/MyInfo'});
        }
      },
      handleOpen(key, keyPath) {
        this.activeIndex = key;
      },
      formatRole(role){
            if(role == 0){
                return '员工'
            }else if(role == 1){
                return '部门经理'
            }else if(role == 2){
                return '副总经理'
            }else if(role == 3){
                return '总经理'
            }else if(role == 4){
                return '人事人员'
            }else if(role == 5){
                return '财务人员'
            }
      },

      loadstate(){
            var empId = window.sessionStorage.getItem('empId');
            this.$http.get('/queryStateById/'+empId).then(res => {
                if (res.data.data==0){
                    this.checkIn = '上班打卡';
                    this.state = '下班';
                    this.stateNum = 0;
                    this.isHere = false;
                }else if (res.data.data==1){
                    this.checkIn = '下班打卡';
                    this.state = '在班';
                    this.stateNum = 1;
                    this.isHere = true;
                }else if (res.data.data==2){
                    this.checkIn = '返回打卡';
                    this.state = '外出';
                    this.stateNum = 2;
                    this.isHere = false;
                }else if (res.data.data==3){
                    this.state = '假期';
                    this.stateNum = 3;
                }else if (res.data.data==4){
                    this.state = '请假';
                    this.stateNum = 4;
                }
            });
        },  
        changeState1(){
            var empId = window.sessionStorage.getItem('empId');
            var num = 0;
            console.log(this.stateNum);
            if(this.stateNum == 0){//下班,变为上班
              num = 0;
            }else if(this.stateNum == 1){//在班，变为下班
              num = 1;
            }else if(this.stateNum == 2){//外出,变为上班
              num = 0;
            }else{//请假
              num = -1;
            }
            this.$http.post('/checkInOrOut/'+empId+'/'+num).then(res => {
                if (res.data.success) {
                    this.$message({
                        message: '操作成功',
                        type: 'success'
                    });
                    this.loadstate();
                } else {
                    this.$message({
                        message: '操作失败',
                        type: 'error'
                    });
                }
            });
        },
        changeState2(){
            var empId = window.sessionStorage.getItem('empId');
            var num = 0;
            console.log(this.stateNum);
            
            this.$http.post('/checkInOrOut/'+empId+'/'+2).then(res => {
                if (res.data.success) {
                    this.$message({
                        message: '操作成功',
                        type: 'success'
                    });
                    this.loadstate();
                } else {
                    this.$message({
                        message: '操作失败',
                        type: 'error'
                    });
                }
            });
        },
    }
  };
</script>

<style scoped>
.personal_logo {
    margin-top: 50px;
    width: 50%;
    height: 50%;
    border-radius: 70%;
}
.personal_position {
    text-align: center;
}
.personal_name {
    text-align: center;
    font-size: 20px;
    color: antiquewhite;
}
.personal_state{
    text-align: center;
    font-size: 16px;
    color: antiquewhite;
}
</style>