<template>
<div>
  <el-breadcrumb separator="/" style="margin-bottom : 20px">
        <el-breadcrumb-item :to="{ path: '#' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item><a href="#">人事管理</a></el-breadcrumb-item>
        <el-breadcrumb-item>添加员工信息</el-breadcrumb-item>
    </el-breadcrumb>
  <div style="margin-top : 50px">
    <div style="float : left;" class="left_style">
      <el-form ref="form" :model="employer" label-width="80px" >
    <el-form-item label="员工姓名">
        <el-input v-model="employer.name" style="width : 200px"></el-input>
    </el-form-item>
    <el-form-item label="员工工号">
        <el-input v-model="employer.account" style="width : 200px"></el-input>
    </el-form-item>
    <el-form-item label="员工性别">
        <el-select v-model="employer.gender" placeholder="请选择性别" style="width : 200px">
            <el-option label="男" value="1"></el-option>
            <el-option label="女" value="0"></el-option>
        </el-select>
    </el-form-item>
    <el-form-item label="员工电话">
        <el-input v-model="employer.phone" style="width : 200px"></el-input>
    </el-form-item>
    <el-form-item label="员工密码">
        <el-input v-model="employer.password" style="width : 200px"></el-input>
    </el-form-item>
    <el-form-item label="员工邮件">
        <el-input v-model="employer.email" style="width : 200px"></el-input>
    </el-form-item>
    <el-form-item label="员工角色" >
        <el-select v-model="employer.role" placeholder="请选择员工类型" style="width : 200px">
            <el-option label="员工" value="0"></el-option>
            <el-option label="部门经理" value="1"></el-option>
            <el-option label="副总经理" value="2"></el-option>
            <el-option label="总经理" value="3"></el-option>
            <el-option label="人事人员" value="4"></el-option>
            <el-option label="财务人员" value="5"></el-option>
        </el-select>
    </el-form-item>
    <!-- <el-form-item label="创建时间">
    <el-col :span="11">
      <el-date-picker type="date" placeholder="选择日期" v-model="employer.createTime" style="width: 100%;"></el-date-picker>
    </el-col>
  </el-form-item> -->
    <el-form-item>
        <el-button type="success" @click="onSubmit">立即创建</el-button>
        <el-button>取消</el-button>
    </el-form-item>
      </el-form>
    </div>
    <div style="float : left" class="right_style">
      <div style="margin-bottom : 10px"><i class="el-icon-user-solid"></i> 员工当前状态</div>
      <el-table
            :data="page"
            stripe=""
            style="width: 750px"
            :default-sort = "{prop: 'time', order: 'descending'}"
            >
        <el-table-column
            prop="name"
            label="用户名"
        >
        </el-table-column>
        <el-table-column
            prop="account"
            label="工号">
        </el-table-column>
        
        <el-table-column
            prop="state"
            label="状态"
            :formatter="formatState"
            >
        </el-table-column>
    </el-table>
    </div>  
  </div>


    </div>
</template>
<script>
  export default {
    data() {
      return {
        employer: {},
        page:[]
      }
    },
    created(){
        this.loaddata();
      },
    methods: {
      onSubmit() {
        this.employer.createTime = this.formatDate()
        console.log(this.formatDate(this.employer.createTime));
        this.$http.post('insertEmpInfo?account='+this.employer.account+'&createTime='+this.employer.createTime+'&email='+this.employer.email+'&gender='+this.employer.gender+'&name='+this.employer.name+'&password='+this.employer.password+'&phone='+this.employer.phone+'&role='+this.employer.role).then(res => {
          if (res.data.success) {
            this.$message({
              message: '添加成功',
              type: 'success'
            });
            this.$router.push('/employer/AllEmployer');
          } else {
            this.$message({
              message: '添加失败',
              type: 'error'
            });
          }
        });
      },
      formatDate() {
        var dt = new Date()
        var year = dt.getFullYear()
        var month = dt.getMonth() + 1
        var date = dt.getDate()
        if (month < 10) {
            month = '0' + month
        }
        if (date < 10) {
            date = '0' + date
        }
        return year + '-' + month + '-' + date
      },
      loaddata(){
            var account = window.sessionStorage.getItem('account');
            this.$http.get('/queryEmpState?account=').then(res => {
                console.log(res.data);
                this.page = res.data.data;
            });
        },
        formatType(row){
            if (row.type == 0) {
                return '上班打卡';
            } else if (row.type == 1) {
                return '下班打卡';
            } else if (row.type == 2) {
                return '外出打卡';
            } else if (row.type == 3) {
                return '返回打卡';
            }
        },
        formatState(row){
            if (row.state == 0) {
                return '下班';
            } else if (row.state == 1) {
                return '在班';
            } else if (row.state == 2) {
                return '外出';
            } else if (row.state == 3) {
                return '假期';
            } else if (row.state == 4) {
                return '请假';
            }
        }
    }
  }
</script>

<style scoped>
.right_style{
  margin-left: 50px;
}
</style>