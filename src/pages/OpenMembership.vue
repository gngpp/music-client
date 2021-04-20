<template>
  <div  style="background-color: #ffffff;
  border-radius: 7px;
  margin: 30px 12%;
  margin-top:150px;
  min-height: 65vh;">
      <div style="margin: 10px;">
        <span style="color: #000000">
        <el-page-header @back="goBack()" content="详情" ></el-page-header>
        </span>
      </div>
    <hr style="margin: 50px;margin-top: 20px"/>
    <div  style="margin-top: 20px;margin: 55px">
      <img  :src="getPic()">
    </div>
    <div style="
     margin: 300px;
     border-radius: 7px;
     margin-top: -420px;
     margin-left: 500px;
      margin-right: 100px">
      <h2>音乐VIP会员</h2>
      <p v-if="isMember === 'false'" >当前未开通VIP/音乐包</p>
      <p v-if="isMember === 'true'">服务到期时间：{{currentTime}}</p>
     <hr style="border:none;height: 10px">
      <p style="font-size: 30px;color: #d33a31;font-style: normal;text-align:left">
        <span >¥</span>
        <em v-if="value===null" style="font-style: normal;text-align: left">16-158</em>
        <em v-if="value!==null" style="font-style: normal;text-align: left">{{value}}</em>
      </p>
    </div>
    <div style="
     background-color: rgba(255,255,255,0);
     margin: 300px;
     margin-top: -290px;
     margin-left: 500px;
      margin-right: 50px">
      <div >
        <p style="padding: 5px"> 选择类型 </p>
      </div>
      <el-radio-group @change="checkRadio()" v-model="radioOne"   fill="#D33A31" class="ok"  style="border-radius: 1px" >
        <el-radio-button label=16>
          <p>黑胶VIP</p>
          <p>黑胶特权+音乐包特权</p>
        </el-radio-button>
        <el-radio-button  label=8>
          <p>音乐包</p>
          <p>&nbsp&nbsp下载量：300首/月 &nbsp&nbsp</p>
        </el-radio-button>
      </el-radio-group>
    </div>
    <div style="
     margin: 300px;
     margin-top: -280px;
     margin-left: 500px;
      margin-right: 110px">
      <div >
        <p style="padding: 5px">
          选择时长
          <i v-if="time!==null" style="font-size: 11px">&nbsp;&nbsp;{{'购买后有效期至：' + time}}</i>
        </p>

      </div>
      <el-radio-group  @change="checkRadio()" v-model="radioTwo" size="mini" fill="#D33A31" style="border-radius: 1px">
        <el-radio-button class="ok"   label=1 size="mini" >
          1个月
        </el-radio-button>
        <i></i>
        <el-radio-button class="ok"   label=3 size="mini" >
          3个月
        </el-radio-button>
        <i></i>
        <el-radio-button class="ok"   label=12 size="mini" >
          12个月
        </el-radio-button>
      </el-radio-group>
      <hr style="margin-top: 20px;background-color: rgba(196,196,196,0.69);border: none;height: 1px">
    </div>
    <div style="
     background-color: rgba(255,255,255,0);
     margin: 300px;
     margin-top: -280px;
     margin-left: 620px;">
      <el-button  @click="check()" style="color: #ffffff;background-color: #d33a31" plain>
        立即开通
      </el-button>
    </div>

    <div style="
     margin: 150px;
     margin-top: -250px;
     margin-right: 56px;
     margin-left: 56px;">
      <h2>商品详情</h2>
      <hr style="margin-top: 20px;background-color: rgba(0,0,0,0.53);border: none;height: 1px">
        <p style="padding: 10px;font-size: 14px">
          <span>
            内容说明：
          </span>
        </p>
      <p>&nbsp&nbsp&nbsp· 买黑胶VIP赠送同等时长音乐包，尊享双重特权；</p>
      <p>&nbsp&nbsp&nbsp· 尊享会员曲库、无损音质、免广告、头像挂件、个性皮肤、歌词图片、专属电台、折扣电台、商城折扣、尊贵身份等十多项黑胶专属特权；</p>
      <p style="padding: 10px;font-size: 14px">
        <span>
          使用说明：
        </span>
      </p>
      <p>&nbsp&nbsp&nbsp· 自充值之时重新登陆生效</p>
      <p>&nbsp&nbsp&nbsp· 充值之后若发现还是无法正常使用，可尝试先清除浏览器缓存重新登陆；</p>
    </div>
    <el-dialog
      title="提示"
      :visible.sync="dialogVisible"
      width="30%"
      :before-close="handleClose">
      <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
        <el-form-item label="卡号" prop="cardNumber">
          <el-input type="text" v-model="ruleForm.cardNumber" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="cardPwd">
          <el-input type="password" v-model="ruleForm.cardPwd" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm('ruleForm')" plain size="mini">提交</el-button>
          <el-button @click="resetForm('ruleForm')" plain size="mini">重置</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>

</template>
<script>
import {mapGetters} from 'vuex'
import {getAfterExpireTime, getCurrentExpireTime, membershipBusiness} from '../api'
export default {
  data () {
    var validateCardNumber = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入充值卡号'));
      } else {
        callback()
      }
    }
    var validateCardPass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入密码'));
      }  else {
        callback()
      }
    }
    return {
      dialogVisible: false,
      radioOne: '16',
      radioTwo: '1',
      value: null,
      pic: 'static/img/109951163338309990.jpg',
      time: null,
      currentTime: null,
      isMember: '',
      ruleForm: {
        username: '',
        cardNumber: '',
        cardPwd: '',
        type: ''
      },
      rules: {
        cardNumber: [
          {validator: validateCardNumber, trigger: 'blur'}
        ],
        cardPwd: [
          {validator: validateCardPass, trigger: 'blur'}
        ]
      }
    }
  },
  computed: {
    ...mapGetters([
      'userId',
      'username',
      'loginIn'
    ])
  },
  created () {
    if (!this.loginIn) {
      this.$router.push({path: 'login-in'})
      this.$router.go(0)
    }
    this.isMember = localStorage.getItem('isMember')
    this.getAfterExpireTime(this.userId, 1)
    if (this.isMember !== 'false') {
      getCurrentExpireTime(this.userId).then(res => {
        let currentTime = new Date(res.data)
        this.currentTime = currentTime.getFullYear() + '-' + (currentTime.getMonth()+1) + '-' +currentTime.getDay()
      }).catch(
        reason => {
          this.isMember = false
        }
      )
    }
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.ruleForm.username = this.username
          this.ruleForm.type = this.getCardType(this.radioTwo)
          membershipBusiness(this.ruleForm)
            .then(res => {
               if (res.status === 200) {
                 this.$notify.success('充值成功,请重新登陆。')
                 setTimeout(() => {
                   this.dialogVisible = false
                   this.$refs['ruleForm'].resetFields();
                 },1000)
               }
            }).catch(() => {
            this.$notify.error('充值卡不存在！')
          })
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    },
    getCardType (types) {
      switch (types) {
        case '1': {
          return 1
        }
        case '3': {
          return 2
        }
        case '12': {
          return 3
        }
      }
    },
    resetForm(formName) {
      // this.$refs[formName].resetFields();
      this.ruleForm.cardNumber = ''
      this.ruleForm.cardPwd = ''
    },
    handleClose(done) {
      this.$confirm('确认关闭？')
        .then(_ => {
          done();
        })
        .catch(_ => {});
    },
    getPic () {
      return this.pic
    },
    goBack () {
      this.$router.go(-1)
    },
    checkRadio () {
      if (this.radioOne === '8') {
        this.pic = 'static/img/109951163350111521.jpg'
        switch (this.radioTwo) {
          case '1': {
            this.value = 8
            this.getAfterExpireTime(this.userId,1)
            break
          }
          case '3': {
            this.value = 24
            this.getAfterExpireTime(this.userId,3)
            break
          }
          case '12': {
            this.value = 88
            this.getAfterExpireTime(this.userId,12)
            break
          }
        }
      } else {
        this.pic = 'static/img/109951163338309990.jpg'
        switch (this.radioTwo) {
          case '1': {
            this.value = 16
            this.getAfterExpireTime(this.userId,1)
            break
          }
          case '3': {
            this.value = 45
            this.getAfterExpireTime(this.userId,3)
            break
          }
          case '12': {
            this.value = 158
            this.getAfterExpireTime(this.userId,12)
            break
          }
        }
      }

    },
    getAfterExpireTime (consumerId, afterMonth) {
      getAfterExpireTime(consumerId,afterMonth).then(res => {
        let expireTime = new Date(res.data);
        this.time = expireTime.getFullYear() + '-' + (expireTime.getUTCMonth() + 1) + '-' + expireTime.getUTCDate()
      })
    },
    check () {
      if (this.radioOne === '8' && this.radioTwo !== null) {
        this.$notify.warning('该服务未上线！')
      } else {
        this.dialogVisible = true
      }
    }
  }
}
</script>
<style lang="scss" scoped>
  .ok {
    border-color:  #cf0505;
    color: #e50000;
  }
  .ok:hover {
    border-color:  #cf0505;
    color: #ca0000;
  }
  .ok:focus {
    border-color:   #cf0505;
    color: #db0000;
  }
</style>
