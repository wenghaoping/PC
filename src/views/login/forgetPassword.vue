<template>
  <div id="forgetPassword">
    <div class="title tc">微天使，FA的智能工作站</div>
    <div class="loginBox">
      <div class="backLogin" @click="backLogin">
        <img class="arrowLeft" src="../../assets/images/goback.png"/>
        返回登录
      </div>
      <input class="telephone" v-model="telephone" placeholder="输入注册时的手机号">
      <div class="codeFrame">
        <input class="code" v-model="captcha" placeholder="请输入验证码">
        <el-button type="text" @click="getCode" v-show="is_getCode==0">获取验证码</el-button>
        <el-button type="text" v-show="is_getCode!=0" :disabled="true">{{captchaNum}}后重新获取</el-button>
      </div>
      <input class="resetCode" type="password" v-model="resetPassword" placeholder="重置密码" min="6" max="20">
      <el-button class="loginBtn tc" @click="next">确定</el-button>
    </div>
  </div>
</template>


<script type="text/ecmascript-6">
  export default {
    data () {
      return {
        telephone:'',
        captcha: '',
        resetPassword:'',
        // 是否点击过获取验证码标识
        is_getCode: 0,
        // 获取验证码的倒计时
        captchaNum: 60,
      }
    },
    methods: {
      //backLogin
      backLogin(){
        this.$router.push({name:'telephoneLogin'})
      },
      // 获取验证码
      getCode(){
        if (!this.$tool.checkPhoneNubmer(this.telephone)) {
          this.$tool.error('请正确输入手机号码')
        } else {
          this.$http.post(this.URL.resetPasswordCaptcha, {
            user_mobile: this.telephone
          }).then(res => {
            if (res.data.status_code == 2000000) {
              this.is_getCode = 1;
              var timer = setInterval(() => {
                this.captchaNum--
              }, 1000)
              setTimeout(() => {
                clearInterval(timer);
                this.captchaNum = 60;
                this.is_getCode = 0;
              }, 60000)
              this.$tool.console('验证码发送成功')
            } else {
              this.$tool.error(res.data.error_msg)
            }
          })
        }
      },
      // 登录
      next(){
        if(!this.$tool.checkPhoneNubmer(this.telephone)){
            this.$tool.error('请正确输入手机号码')
        }else if(this.$tool.getNull(this.captcha)){
          this.$tool.error('请正确输入验证码')
        }else if(this.$tool.getNull(this.resetPassword)){
            this.$tool.error('请正确输入重置密码')
        }else if(this.$tool.checkPassword(this.resetPassword)){
            this.$tool.error('重置密码长度应为6-20')
        } else{
            this.$http.post(this.URL.resetPassword,{
              user_mobile:parseInt(this.telephone),
              captcha:parseInt(this.captcha),
              user_passwd:this.resetPassword,
            }).then(res=>{
                this.$tool.console(res)
              if(res.data.status_code==2000000){
                    this.$router.push({name:"telephoneLogin"})
              }else{
                  this.$tool.error(res.data.error_msg)
              }
            })
        }
      },
    }
  }
</script>

<style scoped lang="less">
  @import "../../assets/css/login.less";
</style>
