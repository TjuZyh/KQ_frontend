<template>
<div>
  <el-breadcrumb separator="/" style="margin-bottom : 20px">
        <el-breadcrumb-item :to="{ path: '#' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item><a href="#">申请假期</a></el-breadcrumb-item>
        <el-breadcrumb-item>添加申请</el-breadcrumb-item>
    </el-breadcrumb>
    <div class="apply_form_div">
        <el-form ref="apply" :model="apply" label-width="100px" class="apply_form">
    <el-form-item label="请假开始时间">
      <el-col :span="10">
      <el-date-picker type="date" placeholder="选择日期" v-model="apply.startTime" style="width: 100%;"></el-date-picker>
      </el-col>
    </el-form-item>
    <el-form-item label="请假天数" style="width : 295px">
        <el-input v-model="apply.duringTime"></el-input>
    </el-form-item>
    
    <el-form-item label="请假类型">
        <el-select v-model="apply.type" placeholder="请选择类型">
            <el-option label="事假" value="0"></el-option>
            <el-option label="年假" value="1"></el-option>
            <el-option label="婚假" value="2"></el-option>
            <el-option label="产检" value="3"></el-option>
            <el-option label="产假" value="4"></el-option>
            <el-option label="哺乳" value="5"></el-option>
            <el-option label="陪产" value="6"></el-option>
            <el-option label="外出" value="7"></el-option>
        </el-select>
    </el-form-item>
    <el-form-item label="请假原因">
        <el-input v-model="apply.reason"></el-input>
    </el-form-item>
    <el-form-item>
        <el-button type="success" @click="onSubmit">申请</el-button>
        <el-button @click="resetForm">重置</el-button>
    </el-form-item>
    </el-form>
    </div>

    <div>
      <i class="el-icon-s-order"></i>
      <span style="color : #0d5056">已经申请的事项</span>
    </div>
    <div style="margin-top : 20px">
    <el-table
            :data="page1"
            
            style="width: 100%"
            :row-class-name="tableRowClassName"
            >
        <el-table-column
            prop="applyId"
            label="申请序号">
        </el-table-column>
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
            prop="duringTime"
            label="请假天数">
        </el-table-column>
        <el-table-column
            prop="state"
            label="请假状态"
            :formatter="formatState"
            >
        </el-table-column>
        <el-table-column
            prop="type"
            label="请假类型"
            :formatter="formatType"
            >
        </el-table-column>
    </el-table>
    </div>

</div>
</template>
<script>
  export default {
    data() {
      return {
        apply: {},
        page1: [],
      }
    },
    created() {
      this.loaddata();
      if(this.$route.query.type!=null){
        this.apply.type = this.$route.query.type;
      }
      
    },
    
    methods: {
      onSubmit() {
        
        var empId = window.sessionStorage.getItem('empId');
        this.apply.startTime = this.apply.startTime.toISOString().substring(0,10) +" "+ this.apply.startTime.toISOString().substring(11,19);
        console.log('applyVacation/'+empId+'/'+this.apply.startTime+'/'+this.apply.duringTime+'/'+this.apply.reason+'/'+this.apply.type);
        this.$http.post('applyVacation/'+empId+'/'+this.apply.startTime+'/'+this.apply.duringTime+'/'+this.apply.reason+'/'+this.apply.type).then(res => {
          if (res.data.code == 200) {
            this.$message({
              message: '添加成功',
              type: 'success'
            });
            this.$router.push('/apply/addApply');
          } else if(res.data.code == 302) {
            this.$message({
              message: res.data.message,
              type: 'error'
            });
          }else{
            this.$message({
              message: res.data.message,
              type: 'error'
            });
          }
        });
        // this.$http.get('queryAllEmployeeInfo').then(res => {
        //   console.log(res);
        // });
      },
      resetForm() {
        this.apply={};
      },
      loaddata(){
        var empId = window.sessionStorage.getItem('empId');
            this.$http.get('/queryApplyingByEmpId/'+empId).then(res => {
                console.log(res.data.data);
                this.page1 = res.data.data;
            });
            
        },
      formatState(row){
            if (row.state == 0) {
                return '拒绝';
            } else if (row.state == 1) {
                return '审核中';
            } else if (row.state == 2) {
                return '通过';
            }
        },
    }
  }
</script>

<style scoped>
.apply_form_div{
  background: rgb(228, 227, 227);
  border-radius: 12px;
  margin-bottom: 20px;
}
.apply_form{
  padding: 15px;
}

</style>