<template>
      <div >
      <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
      <el-button type="danger" size="small">批量删除</el-button>
     <el-tabs v-model="activeName" @tab-click="handleClick">
    <el-tab-pane label="所有订单" name="first">所有订单</el-tab-pane>
    <el-tab-pane label="待支付" name="second">待支付</el-tab-pane>
    <el-tab-pane label="待派单" name="third">待派单</el-tab-pane>
    <el-tab-pane label="待接单" name="fourth">待接单</el-tab-pane>
    <el-tab-pane label="待服务" name="fifth">待服务</el-tab-pane>
    <el-tab-pane label="待确认" name="sixth">待确认</el-tab-pane>
    <el-tab-pane label="已完成" name="seventh">已完成</el-tab-pane>
  </el-tabs>
  <el-table :data="orders">
   <el-table-column prop="id" label=订单编号></el-table-column>
    <el-table-column prop="orderTime" label=联系电话></el-table-column>
    <el-table-column prop="total" label=总价></el-table-column>
    <el-table-column prop="status" label=状态></el-table-column>
    <el-table-column prop="customerId" label=顾客Id></el-table-column>
    <el-table-column prop="waiterId" label=员工Id></el-table-column>
      <el-table-column label=操作>
        <template v-slot="slot">
      <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
      <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
        </template>
      </el-table-column>
  </el-table>

 <el-dialog
  :title="录入订单信息"
  :visible.sync="Visible"
  width="60%"
  >
 ---- {{form}}
  <el-form :model="form" label-width="80px"> 
  <el-form-item label="订单编号">
    <el-input v-model="form.id"/>
    </el-form-item>
     <el-form-item label="联系电话">
    <el-input v-model="form.orderTime"/>
    </el-form-item>
     <el-form-item label="总价">
    <el-input v-model="form.total"/>
    </el-form-item>
     <el-form-item label="状态">
    <el-input v-model="form.statu"/>
    </el-form-item>
     <el-form-item label="顾客Id">
    <el-input v-model="form.customerId"/>
    </el-form-item>
     <el-form-item label="员工Id">
    <el-input v-model="form.waiterId"/>
    </el-form-item>
  </el-form>


        <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>

   </div>
</template>
<script>
import request from '@/utils/request' //自定义库
import querystring from 'querystring' //系统库
export default {
   data(){
      return {
        activeName: 'order',
   Visible:false,         
   orders:[],
   form:{
     type:"order"
   }
        }
    },
        created(){
     //vue实例加载完毕执行的代码
    //  let url="http://localhost:6677/order/findAll"
    //  request.get(url).then((response)=>{
    //    this.orders=response.data;
    //  })
      this.loadData();
    },
methods:{
  submitHandler(){
    //前端向后台发送请求，完成数据的保存操作
    let url="http://localhost:6677/order/save";
    request({
      url,
       method:"post",
       //告诉后台 我的请求体中放的是查询字符串
       headers:{
          "Content-Type":"application/x-www-form-urlencoded"
       },
       data:querystring.stringify(this.form)
    }).then((response)=>{
      this.closeModalHandler();
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
    let url="http://localhost:6677/order/findAll"
     request.get(url).then((response)=>{
       //箭头函数中的this指向外部函数的this
       this.orders=response.data;
     })
  },
toAddHandler() {
     this.form={
       type:"order"
     }
      this.Visible=true;     
},
closeModalHandler(){
    this.Visible=false;
}, 
toUpdataHandler(row){
     this.form=row;
     this.Visible=true;
},
toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
         let url="http://localhost:6677/order/deleteById?id="+id;
        request.get(url).then((response)=>{
            this.loadData();
            this.$message({
            type: 'success',
            message:response.message
        });
        }) 
      }) 
     }
}
}
</script>
<style scoped>

</style>