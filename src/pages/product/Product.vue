<template>
      <div >
      <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
      <el-button type="danger" size="small">批量删除</el-button>

  <el-table :data="products">
    <el-table-column  prop="id" label=编号></el-table-column>
    <el-table-column  prop="name" label=产品名称></el-table-column>
    <el-table-column  prop="price" label=价格></el-table-column>
    <el-table-column  prop="description" width="200px" label=描述></el-table-column>
    <el-table-column prop="categoryId"  width="200px" label=所属产品></el-table-column>
    <el-table-column prop="photo"  label=照片 width="650px"></el-table-column>
      <el-table-column label=操作>
        <template v-slot="slot">
      <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
      <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
        </template>
      </el-table-column>
  </el-table>
   <!-- <div class="block">
    <span class="demonstration"></span>
     <el-pagination
    layout="prev, pager, next"
    :total="1000">
     </el-pagination>
   </div> -->
 <el-dialog
  :title="title"
  :visible.sync="Visible"
  width="60%"
  >
  {{form}}
    <el-form :model="form" label-width="80px"> 
    <el-form-item label="名称">
      <el-input v-model="form.name"/>
      </el-form-item>
      <el-form-item label="价格">
      <el-input  v-model="form.price"/>
      </el-form-item>

        <el-form-item label="所属栏目">
        <el-select v-model="form.categoryId">
      <el-option 
      v-for="item in options" 
      :key="item.id"
      :label="item.name"
      :value="item.id"
      ></el-option></el-select>
      </el-form-item>

      <el-form-item label="介绍">
      <el-input type="textarea" v-model="form.description"/>
      </el-form-item>
      <el-form-item label="图片">
            <el-upload
            class="upload-demo"
            action="https://134.175.154.93:6677/file/upload/"
            :file-list="fileList"
            :on-success="uploadSuccessHandler"
            list-type="picture">
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
          </el-upload>
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
   products:[],
   options:[],
   form:{  
   },
   filelist:[]
        }
    },
        created(){
     //vue实例加载完毕执行的代码
    //  let url="http://localhost:6677/waiter/findAll"
    //  request.get(url).then((response)=>{
    //    this.employees=response.data;
    //  })
      this.loadData();
      this.loadCategory();
    },
methods:{
  //上传成功的事件处理函数
  uploadSuccessHandler(response){
    let url="http://134.175.154.93:8888/group1/"+response.data.id
    //将图片地址设置到form中一起提交给后台
     console.log(response);
  },
  submitHandler(){
    //前端向后台发送请求，完成数据的保存操作
    let url="http://localhost:6677/product/saveOrUpdate";
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
    let url="http://localhost:6677/product/findAll"
     request.get(url).then((response)=>{
       //箭头函数中的this指向外部函数的this
       this.products=response.data;
     })
  },
    loadCategory(){
    let url="http://localhost:6677/category/findAll"
     request.get(url).then((response)=>{
        // 将查询结果设置到products中，this指向外部函数的this
        this.options = response.data;
      })
  },
toAddHandler() {
           this.form={ 
     };
      this.filelist=[];
      this.title="添加产品信息";
      this.Visible=true;
      
},
closeModalHandler(){
    this.Visible=false;
}, 
toUpdataHandler(row){
  this.filelist=[];
     this.form=row;
     this.title="修改产品信息";
     this.Visible=true;
},
toDeleteHandler(id){
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
         let url="http://localhost:6677/product/deleteById?id="+id;
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