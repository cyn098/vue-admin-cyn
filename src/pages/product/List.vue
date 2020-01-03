<template>
    <div>
        <!-- 按钮 -->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button> 
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- /按钮 -->
        <!-- 表格 -->
       <el-table :data="products">
            <el-table-column label="编号" prop="id"></el-table-column>
            <el-table-column label="产品名称" prop="name"></el-table-column>
            <el-table-column label="价格" prop="price"></el-table-column>
            <el-table-column label="描述" prop="discription"></el-table-column>
            <el-table-column label="所属产品" prop="categoryId"></el-table-column>
            <el-table-column label="操作" >
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- /表格结束 -->
        <!-- 分页 -->
        <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
        <!-- /分页 -->
        <!-- 模态框 -->
         <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">  
            测试{{form}}        
            ---{{form}}
             <el-form :model="form" label-width="80px">
                 <el-form-item label="名称">
                     <el-input v-model="form.name"></el-input>
                 </el-form-item>
                 <el-form-item label="价格">
                     <el-input v-model="form.price"></el-input>
                 </el-form-item>
                 <el-form-item label="所属栏目">
                    <el-select v-model="form.status" placeholder="请选择">
                        <el-option v-for="item in options"
                            :key="item.value" :label="item.name"
                            :value="item.parentId">
                        </el-option>
                    </el-select>
                </el-form-item>
             </el-form>    
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
        <!-- /模态框 -->
        <!-- /模态框 -->
    </div>
</template>


<<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    data(){
        return{
            title:"录入产品信息",
            visible:false,
            products:[],
            form:{
                type:"product"
            }
        }
    },
    created(){
        //在页面加载出来时的时候加载数据
        this.loadData();
    },
    methods:{
        submitHandler(){
            let url="http://localhost:6677/product/saveOrUpdate"
            //前端向后台发送请求，完成数据的保存操作
            request({
                url,
                method:"post",
                //告诉给后台我的请求体中放的是查询字符串
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                //请求体中的数据，将this，from转换为查询字符串发送给后台
                data:querystring.stringify(this.form)
            }).then((response)=>{
                this.closeModalHandler();
                this.loadData();
                this.$message({
                    type:"success",
                    message:response.message 
                })     
            })
        },
        //重载员工数据
        loadData(){
            //this->vue实例，通过vue实例访问该实例中数据，methods中
            //this.title/this.toAddHandler
            let url="http://localhost:6677/product/findAll"
            request.get(url).then((response)=>{
            //箭头函数中的this指向外部函数中的this
            this.products=response.data;
            })
        },
        toAddHandler(){
            this.title="录入产品信息";
            this.visible=true;
        },
        toDeleteHandler(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
              confirmButtonText: '确定',
              cancelButtonText: '取消',
              type: 'warning'
            }).then(() => {
              this.$message({
                type: 'success',
                message: '删除成功!'+id
            });
        })
    },
        toUpdateHandler(){
            this.title="修改产品信息";
            this.visible=true;
        },

        closeModalHandler(){
            this.visible=false;
        }
    }
}
</script>

<style scoped>

</style>