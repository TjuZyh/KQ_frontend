<template>
    <div>
        <el-breadcrumb separator="/" style="margin-bottom : 20px">
        <el-breadcrumb-item :to="{ path: '#' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item><a href="#">审批假期</a></el-breadcrumb-item>
        <el-breadcrumb-item>部门经理审核</el-breadcrumb-item>
    </el-breadcrumb>
    
    <el-table
        stripe
        :data="page"
        border>
        <el-table-column
            prop="applyId"
            label="申请序号">
        </el-table-column>
        <el-table-column
            prop="name"
            label="姓名">
        </el-table-column>
        <el-table-column
            prop="account"
            label="工号">
        </el-table-column>
        <el-table-column
            label="请假日期"
            >
            <template slot-scope="scope">
        <i class="el-icon-time"></i>
        <span style="margin-left: 10px">{{ formatDate(scope.row.startTime )}}</span>
      </template>
        </el-table-column>
        <el-table-column
            prop="duringTime"
            label="请假天数">
        </el-table-column>
        <el-table-column
            prop="type"
            label="请假类型"
            :formatter="formatType"
            >
        </el-table-column>
        <el-table-column label="操作">
      <template slot-scope="scope">
        <el-button
          size="mini"
          type="success"
          @click="pass(scope.$index, scope.row)">通过</el-button>
        <el-button
          size="mini"
          type="danger"
          @click="reject(scope.$index, scope.row)">驳回</el-button>
      </template>
    </el-table-column>
    </el-table>
    </div>
</template>

<script>
export default{
    data(){
        return {
            page:[]
        }
    },
    created(){
        this.loaddata();
    },
    methods:{
        loaddata(){
            this.$http.get('/getApplyInfoForEvent').then(res => {
                this.page = res.data.data;
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
        formatDate(row){
            var date = new Date(row.startTime);
            var year = date.getFullYear();
            var month = date.getMonth()+1;
            var day = date.getDate();
            return year+'-'+month+'-'+day;
        },
        formatDate(startTime){
            var date = new Date(startTime);
            var year = date.getFullYear();
            var month = date.getMonth()+1;
            var day = date.getDate();
            return year+'-'+month+'-'+day;
        },
        pass(index, row) {
            var _this = this;
            this.$http.post('/checkApplyInfo/'+row.applyId+'/2').then(res => {
                if (res.data) {
                    _this.$message({
                    message: '已通过',
                    type: 'success'
                    });
                    _this.loaddata();
                } else {
                    _this.$message({
                    message: '通过失败',
                    type: 'error'
                    });
                }
            });
        },
        reject(index, row) {
            var _this = this;
            this.$http.post('/checkApplyInfo/'+row.applyId+'/0').then(res => {
                if (res.data) {
                    _this.$message({
                    message: '已驳回',
                    type: 'success'
                    });
                    _this.loaddata();
                } else {
                    _this.$message({
                    message: '驳回失败',
                    type: 'error'
                    });
                }
            });
        }
    }
}
</script>
