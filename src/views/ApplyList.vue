<template>
<div>
    <el-breadcrumb separator="/" style="margin-bottom : 20px">
        <el-breadcrumb-item :to="{ path: '#' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item><a href="#">申请假期</a></el-breadcrumb-item>
        <el-breadcrumb-item>请假记录</el-breadcrumb-item>
    </el-breadcrumb>
    
    <div style="margin-bottom : 10px">
        <i class="el-icon-s-order"></i>
      <span style="color : #0d5056">我的请假记录</span>
    </div>
    
   <el-table
            :data="page.slice((currentPage-1)*pagesize,currentPage*pagesize)"
            stripe
            style="width: 100%"
            >
        <el-table-column
            prop="applyRecordId"
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



    <div style="margin-top:.2rem;margin-left: .2rem;">
            <el-pagination 
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="currentPage"
                :page-sizes="[5,7,10]"
                :page-size="pagesize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="page.length">
            </el-pagination>
        </div>
</div>

</template>

<style >
.el-table .warning-row {
    background: rgb(243, 172, 170);
  }

.el-table .loading-row {
    background: oldlace;
  }

  .el-table .success-row {
    background: #f0f9eb;
  }
</style>

<script>
export default {
     data(){
          return{
                page:[],
                page1: [],
                currentPage: 1,
		        currentIndex: '',
		        pagesize: 7,
          }
      },
      created(){
        this.loaddata();
      },
      methods:{
        tableRowClassName({row, rowIndex}) {
        if (row.state === 0) {
          return 'warning-row';
        } else if (row.state === 2) {
          return 'success-row';
        }else{
            return 'loading-row';
        }
        },
        loaddata(){
            var empId = window.sessionStorage.getItem('empId');
            this.$http.get('/getApplyRecordById/'+empId).then(res => {
                this.page = res.data.data;
            });
            this.$http.get('/queryApplyingByEmpId/'+empId).then(res => {
                console.log(res);
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
        formatType(row){
            if (row.type == 0) {
                return '事假';
            } else if (row.type == 1) {
                return '年假';
            } else if (row.type == 2) {
                return '婚假';
            } else if (row.type == 3) {
                return '产检';
            } else if (row.type == 4) {
                return '产假';
            } else if (row.type == 5) {
                return '哺乳';
            } else if (row.type == 6) {
                return '陪产';
            } else if (row.type == 7) {
                return '外出';
            } 
        },
        handleSizeChange(val) {
        this.pagesize = val;
        console.log(`每页 ${val} 条`);
        },
        handleCurrentChange(val) {
        this.currentPage= val
        },
      }
 }
  </script>
  