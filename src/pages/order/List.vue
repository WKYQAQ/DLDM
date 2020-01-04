<template>
<div>
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
<el-table :data="order.list">
    <el-table-column prop="id" label="编号"></el-table-column>
    <el-table-column prop="orderTime" label="订单时间"></el-table-column>
    <el-table-column prop="total" label="数量"></el-table-column>
    <el-table-column prop="remark" label="注意"></el-table-column>
    <el-table-column prop="customerId" label="顾客编号"></el-table-column>
    <el-table-column prop="waiterId" label="员工编号"></el-table-column>
    <el-table-column prop="addressId" label="地址编号"></el-table-column>
    <el-table-column label="操作">
        <template v-slot="slot"> <!--solt了解一下-->
            <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
            <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>
    </el-table-column>
</el-table>
 <!-- 分页开始 -->
    <el-pagination 
        layout="prev, pager, next" 
        :total="order.total" 
        @current-change="pageChageHandler">
        </el-pagination>
    <!-- /分页结束 -->
   <!--模态框-->
  <el-dialog
  :title="title"
  :visible.sync="visible"
  width="30%">
  {{form}}
  <el-form :model="form" label-width="80px">
        <el-form-item label="编号">
          <el-input v-model="form.id"></el-input>
        </el-form-item>
        <el-form-item label="订单时间">
          <el-input v-model="form.orderTime"></el-input>
        </el-form-item>
        <el-form-item label="数量">
          <el-input v-model="form.total"></el-input> 
        </el-form-item>
        <el-form-item label="注意">
          <el-input type="remark" v-model="form.remark"></el-input> 
        </el-form-item>
        <el-form-item label="顾客编号">
          <el-input type="customerId" v-model="form.customerId"></el-input> 
        </el-form-item>
        <el-form-item label="员工编号">
          <el-input type="waiterId" v-model="form.waiterId"></el-input> 
        </el-form-item>
        <el-form-item label="地址编号">
          <el-input type="addressId" v-model="form.waiterId"></el-input> 
        </el-form-item>
  </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHandler">取 消</el-button>
    <el-button type="primary" size="small" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
<!--模态框结束-->
</div>
</template>
<script>
import querystring from 'querystring'
import request from '@/utils/request'
export default {
//用于存放网页中需要调用的方法
    methods:{
       pageChageHandler(page){
        // 将params中当前页改为插件中的当前页
        this.params.page = page-1;
        // 加载
        this.loaddata();
    },
      loaddata(){
      let url = "http://localhost:6677/order/queryPage"
      request({
          url,
          method:"post",
          headers:{
              "Content-Type":"application/x-www-form-urlencoded"
          },
          data:querystring.stringify(this.params)
      }).then((response)=>{
          // order为一个对象，其中包含了分页信息，以及列表信息
          this.order = response.data;
      })
    },
      //this.form对象---字符串--->后台
      //通过request有后台进行交互，并且要携带参数
        submitHandler(){
            let url="http://localhost:6677/order/save";
            request({
              url,
              method:"post",
              headers:{
                "Content-Type":"application/x-www-form-urlencoded"
              },
              data:querystring.stringify(this.form)

            }).then((response)=>{
            //请求结束
            this.closeModalHandler();
            this.loaddata();
            this.$message({
              type:"success",
              message:response.message
            })
            })
        },
        toDeleteHandler(id){
          //调用后台接口完成删除操作
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          let url="http://localhost:6677/order/deleteById?id="+id;
          request.get(url).then((response)=>{
                  this.loaddata();
                  this.$message({
                    type: 'success',
                    message: '删除成功!'
          });
          })
          })
        },
        toUpdateHandler(row){
          //在模态框的表中显示当前行信息
            this.form=row;
            this.title="修改订单信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },    
        toAddHandler(){
            this.form={
              type:"order"
            }
            this.visible=true;
            this.title="录入订单信息";
        }
    },
//用于存放要向网页中显示的信息
    data(){
        return{
            title:"添加订单信息",
            visible:false,
            order:{},
            form:{
              type:"order"
            },
            params:{
              page:0,
              pageSize:10
            }
            }
    },
    created(){
        //vue实例创建完毕
        this.loaddata();
    }
}
</script>
<style scoped>

</style>