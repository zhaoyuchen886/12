
<template>
  <div class="login">
    <el-card class="login-card">
      <div class="title">
        <img src="../../assets/img/logo_index.png" alt="">
      </div>
      <el-form ref="loginform" :model="formdata" :rules="rules" class="login-form">
        <el-form-item prop="mobile">
          <el-input v-model="formdata.mobile" placeholder="请输入手机号"></el-input>
        </el-form-item>
        <el-form-item prop="code">
          <el-input v-model="formdata.code" placeholder="请输入验证码" style="width:65%"></el-input>
          <el-button style="float:right">发送验证码</el-button>
        </el-form-item>
        <el-form-item prop="agree">
          <el-checkbox v-model="formdata.agree">我已阅读并同意用户协议和隐私条款</el-checkbox>
        </el-form-item>
        <el-form-item>
          <el-button @click="login" type="primary" style="width:100%">登录</el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script>
export default {
  data () {
    return {
      formdata: {
        mobile: '',
        code: '',
        agree: false
      },
      rules: {
        mobile: [
          { required: true, message: '请输入手机号' },
          { pattern: /^1[3456789]\d{9}$/, message: '请输入合法的手机号' }
        ],
        code: [
          { required: true, message: '请输入验证码' },
          { pattern: /^\d{6}$/, message: '请输入6位数字的验证码' }
        ],
        agree: [
          {
            validator: function (rule, value, callback) {
              value ? callback() : callback(new Error('您必须勾选才能登陆!!!'))
            }
          }
        ]
      }
    }
  },
  methods: {
    login () {
      this.$refs.loginform.validate(isOk => {
        this.$axios({
          url: '/authorizations',
          method: 'post',
          data: this.formdata
        })
          .then(res => {
            window.localStorage.setItem('user-token', res.data.data.token)
            // 登录成功,跳转到首页
            this.$router.push('/')
          })
          .catch(() => {
            // 登录失败,提示信息
            this.$message({
              message: '手机号或者验证码错误',
              type: 'warning'
            })
          })
      })
    }
  }
}
</script>

<style lang="less" scoped>
.login {
  background-image: url("../../assets/img/login_bg.jpg");
  height: 100vh;
  background-size: cover;
  display: flex;
  justify-content: center;
  align-items: center;
  .login-card {
    width: 440px;
    height: 330px;
    .title {
      text-align: center;
      img {
        height: 45px;
      }
    }
  }
}
</style>
