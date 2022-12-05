<template>
<div class="warp">
  <div class="login">
    <div class="login-img"><img src="../assets/imges/logo.jpg" alt=""></div>
    <div class="login-box">
      <el-form 
      status-icon 
      ref="loginFrom" 
      :model="formLabelAlign" 
      label-width="120px" 
      style="max-width: 400px"
      :rules="rules"
      >
        <el-form-item label="username" prop="username">
          <el-input v-model="formLabelAlign.username" clearable />
        </el-form-item>
        <el-form-item label="password" prop="password">
          <el-input v-model="formLabelAlign.password" type="password" clearable  />
        </el-form-item>
        <el-form-item label="code" prop="code">
          <div style="display:flex">
            <el-input v-model="formLabelAlign.code" clearable />
            <img @click="restCode" :src="codeUrl" alt="">
          </div>
        </el-form-item>
        <el-form-item>
          <el-button @click="submitForm(loginFrom)">登陆</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</div>
</template>

<script lang="ts" setup>
import { reactive,ref } from 'vue'
import type {FormInstance,FormRules} from 'element-plus'
import {ElMessage} from 'element-plus'

const loginFrom = ref<FormInstance>()

const formLabelAlign = reactive({
  username: '',
  password: '',
  code: '',
})

const rules = reactive<FormRules>({
  username:[{required:true,message:'请输入用户名',trigger:'blur'}],
  code:[{required:true,message:'请输入验证码',trigger:'blur'}],
  password:[{required:true,message:'请输入密码',trigger:'blur'},{min:6,message:'密码长度至少6位',trigger:'blur'}]
})


const submitForm = (formEl: FormInstance | undefined) => {
  if (!formEl) return
  formEl.validate((valid) => {
    if (valid) {
      console.log('submit!')
      fetch('/api/user/create',{
        method:"post",
        body:JSON.stringify(formLabelAlign),
        headers:{
          'content-type':'application/json'
        }
      }).then(res=>res.json()).then(res=>{
        if(res.code === 1){
          ElMessage({
            message:'登陆成功',type:'success'
          })
        } else {
          ElMessage({
            message:'验证码错误',type:'error'
          })
        }
      })
    } else {
      ElMessage({
        message:'请输入必填项',
        type:'error'
      })
      return false
    }
  })
}


const codeUrl = ref<string>('/api/user/code')

const restCode = () => codeUrl.value = codeUrl.value + '?' + Math.random()

</script>

<style lang="less" scoped>
.warp {
  height: 100vh;
  width: 100vw;
  background: linear-gradient(to right, #91eAe4, #86a8e7, #7f7fd5);
  display: flex;
  align-items: center;
  justify-content: center;

  .login {
    width: 480px;
    height: 320px;
    background-color: rgba(0, 0, 0, 0.1);
    border: 1px solid transparent;
    border-radius: 20px;
    margin-left: 10px;
    margin-right: 10px;
    display: flex;
    flex-direction: column;
    justify-content: center;

    &-img {
      width: 100%;
      height: 120px;
      display: flex;
      align-items: center;
      justify-content: center;
      margin-top: -100px;

      img {
        height: 120px;
        width: 120px;
        border-radius: 50%;
        border: 3px solid #fff;
        box-shadow: 1px 2px 3px 2px #999;
      }
    }

    &-box{
      margin-top: 20px;
    }
  }
}
</style>
