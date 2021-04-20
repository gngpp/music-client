<template>
<div class="signUp-page">
  <loginLogo/>
  <div class="signUp">
    <div class="signUp-head">
      <span>用户注册</span>
    </div>
    <el-form :model="registerForm" status-icon :rules="rules" ref="registerForm" label-width="70px" class="demo-ruleForm">
      <el-form-item prop="username" label="用户名">
        <el-input prefix-icon="el-icon-s-custom" v-model="registerForm.username" placeholder="用户名"></el-input>
      </el-form-item>
      <el-form-item prop="password" label="密码">
        <el-input prefix-icon="el-icon-lock" type="password" placeholder="密码" v-model="registerForm.password"></el-input>
      </el-form-item>
      <el-form-item prop="sex" label="性别">
        <el-radio-group v-model="registerForm.sex">
          <el-radio :label="0">女</el-radio>
          <el-radio :label="1">男</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item prop="phoneNum" label="手机" >
        <el-input  prefix-icon="el-icon-phone" placeholder="手机" v-model="registerForm.phoneNum"></el-input>
      </el-form-item>
      <el-form-item prop="email" label="邮箱">
        <el-input prefix-icon="el-icon-message" v-model="registerForm.email" placeholder="邮箱"></el-input>
      </el-form-item>
      <el-form-item prop="birth" label="生日">
        <el-date-picker prefix="el-icon-date" type="date" placeholder="选择日期" v-model="registerForm.birth" style="width: 100%;" value-format="yyyy-MM-dd HH:mm:ss" format="yyyy-MM-dd"></el-date-picker>
      </el-form-item>
      <el-form-item prop="introduction" label="签名">
        <el-input  prefix-icon="el-icon-chat-line-square" type="textarea" placeholder="签名" v-model="registerForm.introduction" ></el-input>
      </el-form-item>
      <el-form-item prop="location" label="地区">
        <el-select popper-class="el-icon-location-outline" v-model="registerForm.location" placeholder="地区" style="width:100%">
          <el-option v-for="item in cities" :key="item.value" :label="item.label" :value="item.value"></el-option>
        </el-select>
      </el-form-item>
      <div class="login-btn">
        <el-button size="mini"  @click="goback(-1)">取消</el-button>
        <el-button size="mini" type="primary" @click="SignUp" icon="el-icon-thumb" plain :loading="loading">确定</el-button>
      </div>
    </el-form>
  </div>
</div>
</template>

<script>
import loginLogo from '../components/LoginLogo'
import { mixin } from '../mixins'
import { rules, cities } from '../assets/data/form'
import { SignUp } from '../api/index'

export default {
  name: 'SignUp-page',
  components: {
    loginLogo
  },
  mixins: [mixin],
  data () {
    return {
      registerForm: { // 注册
        username: '',
        password: '',
        sex: '',
        phoneNum: '',
        email: '',
        birth: '',
        introduction: '',
        location: ''
      },
      rules: {},
      cities: [],
      loading: false
    }
  },
  created () {
    this.rules = rules
    this.cities = cities
  },
  methods: {
    SignUp () {
      let _this = this
      SignUp(this.registerForm)
        .then(res => {
          setTimeout(function () {
            this.loading = false
          }, 1000)
          if (res.status === 200) {
            _this.notify('注册成功', 'success')
            setTimeout(function () {
              _this.$router.push({path: '/'})
            }, 2000)
          }
        }).catch((err) => {
        if (err.data.errCode === 401) {
          this.notify('用户已存在', 'error')
        } else if (err.data.errCode === 402) {
          this.notify('邮箱已存在', 'error')
        } else if (err.data.errCode === 403) {
          this.notify('手机号已存在', 'error')
        }
      })
    },
    goback (index) {
      this.$router.go(index)
    }
  }
}
</script>

<style lang="scss" scoped>
@import '../assets/css/sign-up.scss';
</style>
