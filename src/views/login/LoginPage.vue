<script setup>
import { User, Lock } from '@element-plus/icons-vue'
import { ref, watch } from 'vue'
import { userRegisterService, userLoginService } from '@/api/user'
import { ElMessage } from 'element-plus'
import { useUserStore } from '@/stores/user'
import { useRouter } from 'vue-router'
const isRegister = ref(false)
const form = ref()
const userStore = useUserStore()
const router = useRouter()

// 注册
const formModel = ref({
  username: '',
  password: '',
  repassword: ''
})
// 整个表单的规则
const rules = {
  // 非空校验 required: true   message消息提示，   trigger触发校验的时机 blur失焦的时候触发  change实时触发
  username: [
    {
      required: true,
      message: '请输入用户名',
      trigger: 'blur'
    },
    {
      min: 5,
      max: 10,
      message: '用户名必须是5-10位的字符',
      trigger: 'blur'
    }
  ],
  password: [
    {
      required: true,
      message: '请输入密码',
      trigger: 'blur'
    },
    {
      pattern: /^\S{6,15}$/,
      message: '密码必须是6-15位 的非空字符',
      trigger: 'blur'
    }
  ],
  repassword: [
    {
      required: true,
      message: '请输入密码',
      trigger: 'blur'
    },
    {
      pattern: /^\S{6,15}$/,
      message: '密码必须是6-15位 的非空字符',
      trigger: 'blur'
    },
    {
      validator: (rule, value, callback) => {
        if (value !== formModel.value.password) {
          callback(new Error('两次输入密码不一致!'))
        } else {
          callback()
        }
      },
      trigger: 'blur'
    }
  ]
}

const register = async () => {
  // 注册成功之前，先进行预校验，校验成功 -> 请求，校验失败 -> 自动提示
  await form.value.validate()
  await userRegisterService(formModel.value)
  ElMessage.success('注册成功!')
  isRegister.value = false
}
// apifoxshu 111111
const login = async () => {
  await form.value.validate()
  const res = await userLoginService(formModel.value)
  userStore.setToken(res.data.token)
  ElMessage.success('登录成功')
  router.push('/')
}

watch(isRegister, () => {
  formModel.value = {
    username: '',
    password: '',
    repassword: ''
  }
})
</script>

<template>
  <!-- el-row表示一行,一行分成24份
       el-col表示列 :span="12" 表示在一行中,占12份(50%)
  -->
  <el-row class="login-page">
    <el-col :span="12" class="bg"></el-col>
    <el-col :span="6" :offset="3" class="form">
      <!-- 注册相关的 -->
      <el-form
        ref="form"
        :model="formModel"
        :rules="rules"
        size="large"
        autocomplete="off"
        v-if="isRegister"
      >
        <el-form-item>
          <h1>注册</h1>
        </el-form-item>
        <el-form-item prop="username">
          <el-input
            v-model="formModel.username"
            :prefix-icon="User"
            placeholder="请输入用户名"
          ></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input
            :prefix-icon="Lock"
            v-model="formModel.password"
            type="password"
            placeholder="请输入密码"
          ></el-input>
        </el-form-item>
        <el-form-item prop="repassword">
          <el-input
            :prefix-icon="Lock"
            v-model="formModel.repassword"
            type="password"
            placeholder="请输入再次密码"
          ></el-input>
        </el-form-item>
        <el-form-item>
          <el-button class="button" @click="register" type="primary" auto-insert-space>
            注册
          </el-button>
        </el-form-item>
        <el-form-item class="flex">
          <el-link type="info" :underline="false" @click="isRegister = false"> ← 返回 </el-link>
        </el-form-item>
      </el-form>

      <!-- 登录相关的 -->
      <el-form ref="form" :model="formModel" :rules="rules" size="large" autocomplete="off" v-else>
        <el-form-item>
          <h1>登录</h1>
        </el-form-item>
        <el-form-item prop="username">
          <el-input
            :prefix-icon="User"
            v-model="formModel.username"
            placeholder="请输入用户名"
          ></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input
            name="password"
            v-model="formModel.password"
            :prefix-icon="Lock"
            type="password"
            placeholder="请输入密码"
          ></el-input>
        </el-form-item>
        <el-form-item class="flex">
          <div class="flex">
            <el-checkbox>记住我</el-checkbox>
            <el-link type="primary" :underline="false">忘记密码？</el-link>
          </div>
        </el-form-item>
        <el-form-item>
          <el-button class="button" type="primary" @click="login" auto-insert-space>
            登录
          </el-button>
        </el-form-item>
        <el-form-item class="flex">
          <el-link type="info" :underline="false" @click="isRegister = true"> 注册 → </el-link>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>

<style lang="scss" scoped>
.login-page {
  height: 100vh;
  background-color: #fff;
  .bg {
    background:
      url('@/assets/logo2.png') no-repeat 60% center / 240px auto,
      url('@/assets/login_bg.jpg') no-repeat center / cover;
    border-radius: 0 20px 20px 0;
  }
  .form {
    display: flex;
    flex-direction: column;
    justify-content: center;
    user-select: none;
    .title {
      margin: 0 auto;
    }
    .button {
      width: 100%;
    }
    .flex {
      width: 100%;
      display: flex;
      justify-content: space-between;
    }
  }
}
</style>
