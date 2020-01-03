<template>
      <div >
      <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
      <el-button type="danger" size="small">批量删除</el-button>

  <el-table :data="employees">
    <el-table-column fixed="left" prop="id" label=编号></el-table-column>
    <el-table-column   fixed="left" prop="realname" label=用户名></el-table-column>
    <el-table-column  prop="realname" label=姓名></el-table-column>
    <el-table-column  prop="gender" label=性别></el-table-column>
    <el-table-column prop="telephone" width="120" label=手机号></el-table-column>
    <el-table-column prop="idCard" width="200" label=身份证号></el-table-column>
    <el-table-column prop="bankCard" width="200" label=银行卡号></el-table-column>
    <el-table-column prop="" fixed="right" label=操作></el-table-column>
      <el-table-column label=操作>
        <template v-sort="slot">
      <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
      <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
        </template>
      </el-table-column>
  </el-table>

   <div class="block">
    <span class="demonstration"></span>
     <el-pagination
    layout="prev, pager, next"
    :total="1000">
     </el-pagination>
   </div>
 <el-dialog
  :title="title"
  :visible.sync="Visible"
  width="60%"
  >
  {{form}}
  <el-form :model="form" label-width="80px"> 
  <el-form-item label="用户名">
    <el-input v-model="form.username"/>
    </el-form-item>
      <el-form-item label="密码">
    <el-input type="password" v-model="form.password"/>
    </el-form-item>
      <el-form-item label="姓名">
    <el-input v-model="form.realname"/>
    </el-form-item>
      <el-form-item label="性别">
        <el-radio-group v-model="form.gender">
      <el-radio  label="男">男</el-radio>
      <el-radio  label="女">女</el-radio>
        </el-radio-group>
    </el-form-item>
      <el-form-item label="身份证号">
    <el-input v-model="form.idCard"/>
    </el-form-item>
      <el-form-item label="手机号">
    <el-input v-model="form.telephone"/>
    </el-form-item>
      <el-form-item label="银行卡号">
    <el-input v-model="form.bankCard"/>
    </el-form-item>
  </el-form>


  <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHandler" >取 消</el-button>
    <el-button  size="small" type="primary" @click="submitHandler">确 定</el-button>
  </span>
  </el-dialog>

   </div>
</template>
<script>
import request from '@/utils/request' //自定义库
import querystring from 'querystring' //系统库
export default {
   data(){
   return{
   title:"",
   Visible:false,         
   employees:[],
   form:{
     type:"waiter"
   }
        }
    },
        created(){
     //vue实例加载完毕执行的代码
    //  let url="http://localhost:6677/waiter/findAll"
    //  request.get(url).then((response)=>{
    //    this.employees=response.data;
    //  })
      this.loadData();
    },
methods:{
  submitHandler(){
    //前端向后台发送请求，完成数据的保存操作
    let url="http://localhost:6677/waiter/saveOrUpdate";
    request({
      url,
       method:"post",
       //告诉后台 我的请求体中放的是查询字符串
       headers:{
          "Content-Type":"application/x-www-form-urlencoded"
       },
       data:querystring.stringify(this.form)
    }).then((response)=>{
      this.closeModalHandler()
      this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
        })
    })
  },
  loadData(){
   // this->vue实例 可以通过vue实例访问method 也可以是data属性
    let url="http://localhost:6677/waiter/findAll"
     request.get(url).then((response)=>{
       //箭头函数中的this指向外部函数的this
       this.employees=response.data;
     })
  },
toAddHandler() {
     this.form={
       type:"customeer"
     }
      this.title="添加员工信息";
      this.Visible=true;
      
},
closeModalHandler(){
    this.Visible=false;
}, 
toUpdataHandler(row){
     this.form=row;
     this.title="修改员工信息";
     this.Visible=true;
},
toDeleteHandler(id){
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        })
}
    }
}
</script>
<style scoped>

</style>