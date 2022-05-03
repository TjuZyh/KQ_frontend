<template>
<div>
    <el-descriptions class="margin-top" :title='"我的信息 : " + page.name'  :column="3"  border>
    <el-descriptions-item>
      <template slot="label">
        <i class="el-icon-user"></i>
        用户ID
      </template>
        <span>{{page.empId}}</span>
    </el-descriptions-item>
    <el-descriptions-item>
      <template slot="label">
        <i class="el-icon-user"></i>
        用户名
      </template>
        <span>{{page.name}}</span>
    </el-descriptions-item>
    <el-descriptions-item>
      <template slot="label">
        <i class="el-icon-mobile-phone"></i>
        手机号
      </template>
        <span>{{page.phone}}</span>
    </el-descriptions-item>
    <el-descriptions-item>
      <template slot="label">
        <i class="el-icon-time"></i>
        入职时间
      </template>
        <span>{{formatDate(page.createTime)}}</span>
    </el-descriptions-item>
    <el-descriptions-item>
      <template slot="label">
        <i class="el-icon-tickets"></i>
        角色
      </template>
      <el-tag size="small">{{formatRole(page.role)}}</el-tag>
    </el-descriptions-item>
    <el-descriptions-item>
      <template slot="label">
        <i class="el-icon-message"></i>
        邮箱
      </template>
      {{page.email}}
    </el-descriptions-item>
    <el-descriptions-item>
      <template slot="label">
        <i class="el-icon-male"></i>
        性别
      </template>
      {{formatGender(page.gender)}}
    </el-descriptions-item>
    <el-descriptions-item>
      <template slot="label">
        <i class="el-icon-loading"></i>
        密码
      </template>
      {{page.password}}
    </el-descriptions-item>
  </el-descriptions>

  <el-descriptions :title='"我的剩余假期 "'  :column="3"  border style="margin-top : 20px">
    <el-descriptions-item :column="3" >
      <template slot="label">
        <i class="el-icon-time"></i>
        剩余年假
      </template>
        <span>{{leftTime.leftYear}}</span>
        <el-button :disabled='!leftTime.leftYear!=0' type="success" size="medium"  @click="toApply(1)" style="margin-left : 100px">请年假</el-button>
    </el-descriptions-item>
    <el-descriptions-item>
      <template slot="label">
        <i class="el-icon-time"></i>
        剩余婚假
      </template>
        <span>{{leftTime.leftHunJia}}</span>
        <el-button :disabled='!leftTime.leftHunJia!=0' type="success" size="medium"  @click="toApply(2)" style="margin-left : 100px">请婚假</el-button>
    </el-descriptions-item>
    <el-descriptions-item>
      <template slot="label">
        <i class="el-icon-time"></i>
        剩余哺乳假
      </template>
        <span>{{leftTime.leftBuRu}}</span>
        <el-button :disabled='!leftTime.leftBuRu!=0' type="success" size="medium"  @click="toApply(5)" style="margin-left : 100px">请哺乳假</el-button>
    </el-descriptions-item>
    <el-descriptions-item>
      <template slot="label">
        <i class="el-icon-time"></i>
        剩余产假
      </template>
        <span>{{leftTime.leftChanJia}}</span>
        <el-button :disabled='!leftTime.leftChanJia!=0' type="success" size="medium"  @click="toApply(4)" style="margin-left : 100px">请产假</el-button>
    </el-descriptions-item>
    <el-descriptions-item>
      <template slot="label">
        <i class="el-icon-time"></i>
        剩余产检假
      </template>
      <span>{{leftTime.leftChanJian}}</span>
      <el-button :disabled='!leftTime.leftChanJian!=0' type="success" size="medium"  @click="toApply(3)" style="margin-left : 100px">请产检假</el-button>
    </el-descriptions-item>
    <el-descriptions-item>
      <template slot="label">
        <i class="el-icon-time"></i>
        剩余陪产假
      </template>
      {{leftTime.leftPeiChan}}
      <el-button :disabled='!leftTime.leftPeiChan!=0' type="success" size="medium"  @click="toApply(6)" style="margin-left : 100px">请陪产假</el-button>
    </el-descriptions-item>
  </el-descriptions>


  <el-calendar v-model="value" style="margin-top : 20px"></el-calendar>

</div>

</template>

<script>
export default {
    data(){
        return{
            seach:{
                account:'0',
            },
            page:{},
            value: new Date(),
            leftTime:{},
        }
    },
    created(){
        this.loaddate();
    },
    methods:{
        loaddate(){
            this.seach.account = window.sessionStorage.getItem('empId');
            console.log(this.seach.account);
            this.$http.get('/queryEmpInfoById/'+this.seach.account).then(res => {
              console.log(res.data.data);
                this.page = res.data.data;
            });

            var empId = window.sessionStorage.getItem('empId');
            this.$http.get('/getLeftTimeById/'+empId).then(res => {
                console.log(res.data);
                this.leftTime = res.data.data;
            });



        },
        formatDate(startTime){
            var date = new Date(startTime);
            var year = date.getFullYear();
            var month = date.getMonth()+1;
            var day = date.getDate();
            return year+'-'+month+'-'+day;
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
        formatGender(gender){
            if(gender == 0){
                return '女'
            }else{
                return '男'
            }
        },
        toApply(num){
            this.$router.push({path: '/apply/addApply', query: {type: num}});
            // this.$router.push('/apply/addApply');
        }
        
    },
}
</script>