<template>
<!--按钮-->
<div>
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
<!--/按钮-->
<!--表格-->
<el-table :data="product"><!--为是啥用：data？答：详情element，表格-->
    <el-table-column fixed="left" prop="categoryId" label="分类编号"></el-table-column>
    <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
    <el-table-column fixed="left" prop="name" label="产品名"></el-table-column>
    <el-table-column fixed="left" prop="price" label="价格"></el-table-column>
    <el-table-column prop="status" label="产品状态"></el-table-column>
    <el-table-column width="120" prop="description" label="产品说明"></el-table-column>
    <el-table-column width="200" prop="photo" label="产品照片">
        <template   slot-scope="scope">            
                    <img :src="scope.row.photo"  min-width="70" height="70" />
                 </template>   
    </el-table-column>
    <el-table-column fixed="right" label="操作">
        <template v-slot="slot">
            <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
            <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>   
    </el-table-column>
</el-table>
<!--/表格-->
<!--分页-->
<el-pagination
    layout="prev, pager, next"
    :total="1000">
  </el-pagination>
<!--/分页-->
<!--模态框-->
  <el-dialog
  :title="title"
  :visible.sync="visible"
  width="30%">
    {{form}}
  <el-form :model="form" label-width="80px">
            <el-form-item label="产品名">
         <el-input type="name" v-model="form.name"/>
      </el-form-item>
            <el-form-item label="产品状态">
              <el-radio-group v-model="form.status">
    <el-radio label="正常">正常</el-radio>
    <el-radio label="不正常">不正常</el-radio>
  </el-radio-group>
        </el-form-item>
            <el-form-item label="产品说明">
          <el-input v-model="form.description"/>
        </el-form-item>
            <el-form-item label="价格">
          <el-input v-model="form.price"/>
        </el-form-item>
                    <el-form-item label="产品照片">
          <el-input  v-model="form.photo"/>
        </el-form-item>    
  </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHandler">取 消</el-button>
    <el-button type="primary" size="small" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
<!--/模态框-->
</div>  
</template>
<script>
import querystring from 'querystring'
import request from "@/utils/request"
export default {
    methods:{
        upLoad() {
      // 触发上传图片按钮
      this.$refs.avatarInput.dispatchEvent(new MouseEvent("click"));
    },
        submitHandler(){
            let url="http://localhost:6677/product/saveOrUpdate"
            //前端后台发送请求，完成数据的保存操作
            request({
                url,
                method:"POST",
                //告诉给后台我的请求中放的是查询字符串
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                //请求体中的数据,将this.form转换未查询字符串发送给后台
                data:querystring.stringify(this.form)}).then((response)=>{
            //请求结束
            this.closeModalHandler();
            this.loaddata();
            this.$message({
              type:"success",
              message:response.message
            })   
                })
        },
        loaddata(){
            let url = "http://localhost:6677/product/findAll"
            request.get(url).then((response)=>{
                this.product=response.data;
            })
        },
        toDeleteHandler(id){
          //调用后台接口完成删除操作
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          let url="http://localhost:6677/product/deleteById?id="+id;
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
            this.title="修改产品信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },  
        toAddHandler(){
            this.title="录入产品信息";
            this.visible=true;
        }
    },
    data(){
        return{
            visible:false,
            product:[],
            form:{
                type:"product"
            },
            title:"添加产品"

        }
    },
    created(){
        this.loaddata();
    }
}
</script>
<style scoped>

</style>