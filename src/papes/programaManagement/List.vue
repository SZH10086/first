<template>
    <div>
        <el-button type="primary" @click="toAddHandler" size="small">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <el-table :data="programaManagements">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="栏目名称"></el-table-column>
            <el-table-column prop="num" label="序号"></el-table-column>
            <el-table-column prop="parentId" label="父栏目"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeletHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
    <el-pagination layout="prev, pager, next" :total="50">
    </el-pagination>
    <el-dialog :title="title" :visible.sync="visible" width="60%">
      --{{form}}
      <el-form :model="form" label-width="80px">
        <el-form-item label="栏目名称">
          <el-input v-model="form.name"/>
        </el-form-item>
        <el-form-item label="序号">
          <el-input v-model="form.num"/>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="closeModalHandler">取 消</el-button>
        <el-button type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>

    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    created(){
        this.loadData()
    },
    data(){
        return{
            title:"录入栏目信息",
            visible:false,
            programaManagements:[],
            form:{
                type:"category"
            }
        }
    },
    methods:{
        submitHandler(){
            let url = "http://134.175.154.93:6677/category/saveOrUpdate"
            request({
                url,
                method:"post",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
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
        loadData(){
            let url = "http://134.175.154.93:6677/category/findAll"
            request.get(url).then((response)=>{
                this.programaManagements = response.data;
            })
        },
        closeModalHandler(){
            this.visible=false
        },
        toDeletHandler(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
                }).then(() => {
                    let url = "http://134.175.154.93:6677/category/deleteById?id="+id;
                    request.get(url).then((response)=>{
                        this.loadData();
                        this.$message({
                            type: 'success',
                            message:response.message
                });
                    })
                })
        },
        toUpdateHandler(row){
            this.title="修改栏目信息"
            this.visible=true
        },
        toAddHandler(){
            this.title="添加栏目信息"
            this.visible=true;
        }
    },
    
}
</script>


<style scoped>

</style>