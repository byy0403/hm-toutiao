<template>
  <div class="container">
    <el-card class="my-card">
      <img src="../../assets/images/logo_index.png" alt />
      <el-form ref="loginForm" :model="loginForm" :rules="loginrules">
        <el-form-item placeholder="请输入手机号" prop="mobile">
          <el-input v-model="loginForm.mobile"></el-input>
        </el-form-item>
        <el-form-item placeholder="请输入验证码" prop="code">
          <el-input v-model="loginForm.code" style="width:236px;"></el-input>
          <el-button style="float:right">发送验证码</el-button>
        </el-form-item>
        <el-checkbox :value="true">我已阅读并同意用户协议和隐私条款</el-checkbox>
        <el-button type="primary" style="width:100%; margin-top:15px" @click="login()">登录</el-button>
      </el-form>
    </el-card>
  </div>
</template>

<script>
export default {
  data () {
    const checkMoile = (rule, value, callback) => {
      if (!/^1[3-9]\d{9}$/.test(value)) {
        return callback(new Error('手机号格式不对'))
      }
      callback()
    }
    return {
      loginForm: {
        mobile: '13911111111',
        code: '246810'
      },
      loginrules: {
        mobile: [
          { required: true, message: '请输入手机号', trigger: 'blur' },
          { validator: checkMoile, trigger: 'blur' }
        ],
        code: [
          { required: true, message: '请输入验证码', trigger: 'blur' },
          { len: 6, message: '长度为6位', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    login () {
      // 对整个表单进行校验
      this.$refs.loginForm.validate(valid => {
        if (valid) {
          this.$http
            .post(
              'http://ttapi.research.itcast.cn/mp/v1_0/authorizations',
              this.loginForm
            )
            .then(res => {
              this.$router.push('/')
            })
            .catch(() => {
              this.$message.error('手机号或验证码不正确')
            })
        }
      })
    }
  }
}
</script>

<style scoped lang='less' >
.container {
  position: absolute;
  width: 100%;
  height: 100%;
  background: url(../../assets/images/login_bg.jpg) no-repeat center / cover;
  .my-card {
    position: absolute;
    width: 400px;
    height: 340px;
    left: 50%;
    right: 50%;
    transform: translate(-50%, 40%);
    img {
      margin: 10px auto;
      width: 200px;
      display: block;
    }
  }
  .el-form {
    .el-checkbox {
      line-height: 50px;
      display: block;
    }
  }
}
</style>
