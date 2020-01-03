<template>
  <div>
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
    <el-table :data="customers">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="realname" label="姓名"></el-table-column>
      <!-- <el-table-column prop="gender" label="性别"></el-table-column> -->
      <el-table-column prop="telephone" label="联系方式"></el-table-column>
      <el-table-column label="操作">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeletHandler(slot.row.id)">删除</a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      layout="prev, pager, next"
      :total="50">
    </el-pagination>
    <el-dialog
      title="录入顾客信息"
      :visible.sync="visible"
      width="60%">
      <el-form :model="form" label-width="80px">
        <el-form-item label="用户名">
          <el-input v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input type="password" v-model="form.password"></el-input>
        </el-form-item>
        <el-form-item label="真实姓名">
          <el-input v-model="form.realname"></el-input>
        </el-form-item>
        <el-form-item label="手机号">
          <el-input v-model="form.telephone"></el-input>
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
import { type } from 'os';
export default {
  methods:{
    toDeletHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
        }).then(() => {
          let url = "http://134.175.154.93:6677/customer/deleteById?id="+id;
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
      this.visible = true;
      this.form = row;
    },
    closeModalHandler(){
      this.visible = false;
    },
    toAddHandler(){
      this.form={
        type:"customer"
      }
      this.visible = true;
    },
    submitHandler(){
      let url = "http://134.175.154.93:6677/customer/saveOrUpdate"
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
      let url = "http://134.175.154.93:6677/customer/findAll"
      request.get(url).then((response)=>{
      this.customers = response.data;
    })
    }
  },
  // 用于存放要向网页中显示的数据
  data(){
    return{
      visible:false,
      customers:[],
      form:{
        type:"customer"
      }
    }
  },
  created(){
    // vue实例创建完毕
    this.loadData()
  }
}
</script>

<style scoped>

</style>