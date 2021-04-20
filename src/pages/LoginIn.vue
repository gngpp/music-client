<template>
<div class="login-in">
  <login-logo/>
  <div class="login">
    <div class="login-head">
      <span>帐号登录</span>
    </div>
    <el-form
      :model="loginForm"
      status-icon
      :rules="rules"
      ref="loginForm"
      class="demo-ruleForm" >
      <el-form-item prop="username" >
        <el-input prefix-icon="el-icon-s-custom" placeholder="用户名" v-model="loginForm.username" clearable></el-input>
      </el-form-item>
      <el-form-item prop="password">
        <el-input   prefix-icon="el-icon-lock" type="password" placeholder="密码" v-model="loginForm.password" @keyup.enter.native="loginIn" clearable></el-input>
      </el-form-item>
      <el-form-item prop="verifyCode">
        <el-input
          prefix-icon="el-icon-s-check"
          size="mini"
          ref="verifyCode"
          v-model="loginForm.verifyCode"
          placeholder="验证码"
          name="verifyCode"
          type="text"
          tabindex="3"
          autocomplete="on"
          style="width: 50%;"
          clearable
        />
        <img :src="imgCodeUrl" @click="changeImgCode" style="height: 65%;width: 44.5%;position: absolute;left: 165px;top: 8px">
      </el-form-item>
      <div>
        <el-checkbox  style="left: 1px ;top: -12px" v-model="loginForm.isRememberMe" >记住我</el-checkbox>
      </div>
      <div class="login-btn">
        <el-button size="mini"  @click="goSignUp()" icon="el-icon-user" plain>注册</el-button>
        <el-button size="mini" type="primary" @click="handleLoginIn" icon="el-icon-thumb" plain :loading="this.loading">登录</el-button>
      </div>
    </el-form>
  </div>
</div>
</template>

<script>
import { mixin } from '../mixins'
import LoginLogo from '../components/LoginLogo'
import { loginIn } from '../api/index'
import {mapGetters} from "vuex";

export default {
  name: 'login-in',
  components: {
    LoginLogo
  },
  computed: {
    ...mapGetters([
      'loginIn'
    ])
  },
  mixins: [mixin],
  created () {
    if (!this.loginIn) {
      this.notify('请先登录', 'warning')
    }
    this.changeImgCode()
  },
  data: function () {
    let validateName = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('用户名不能为空'))
      } else {
        callback()
      }
    }
    let validatePassword = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入密码'))
      } else {
        callback()
      }
    }
    return {
      loginForm: { // 登录用户名密码
        username: null,
        password: null,
        verifyCode: '',
        randomCode: '',
        isRememberMe: false
      },
      rules: {
        username: [
          { validator: validateName, message: '请输入用户名', trigger: 'blur' }
        ],
        password: [
          { validator: validatePassword, message: '请输入密码', trigger: 'blur' }
        ]
      },
      imgCodeUrl: '',
      loading: false
    }
  },
  mounted () {
    this.changeIndex('登录')
    setInterval(this.timerJob, 60000)
  },
  methods: {
    timerJob () {
      this.changeImgCode()
    },
    changeImgCode () {
      var num = Math.ceil(Math.random() * 10)
      this.loginForm.randomCode = ''
      this.imgCodeUrl = ''
      this.loginForm.randomCode = num
      this.imgCodeUrl = this.$store.state.configure.HOST + '/api/app/verifyCode/' + num
    },
    changeIndex (value) {
      this.$store.commit('setActiveName', value)
    },
    handleLoginIn () {
      this.loading = true
      let _this = this
      loginIn(this.loginForm)
        .then(res => {
          setTimeout(function () {
            _this.loading = false
          }, 1000)
          if (res.status === 200) {
            _this.notify('登陆成功', 'success')
            _this.setUserMsg(res.data)
            _this.$store.commit('setLoginIn', true)
            setTimeout(function () {
              _this.changeIndex('首页')
              _this.$router.push({path: '/'})
              _this.$router.go(0)
            }, 1500)
          } else {
            this.loading = false
            this.changeImgCode()
            _this.notify('用户名或密码错误', 'error')
          }
        })
        // eslint-disable-next-line handle-callback-err
        .catch(error => {
          this.changeImgCode()
          _this.notify('验证码错误', 'error')
          this.loading = false
        })
    },
    setUserMsg (item) {
      this.$store.commit('setUserId', item.id)
      this.$store.commit('setUsername', item.username)
      this.$store.commit('setAvatar', item.avatar)
      this.$store.commit('setMember', item.isMember)
      this.$store.commit('setToken', 'Bearer ' + item.token)
    },
    goSignUp () {
      this.$router.push({path: '/sign-up'})
    }
  }
}
</script>

<style lang="scss" scoped>
@import '../assets/css/login-in.scss';
</style>
