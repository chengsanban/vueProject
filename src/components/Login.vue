<template>
  <div class="login_box">
    <div class="login_form">
      <div class="logo">
        <img src="../assets/logo.png" alt="logo" />
      </div>
      <el-form ref="loginFromRef" label-width="0px" :model="loginFrom" :rules="loginRules">
        <el-form-item prop="username">
          <el-input v-model="loginFrom.username"></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input v-model="loginFrom.password"></el-input>
        </el-form-item>
        <el-form-item class="btns">
          <el-button type="primary" @click="login">登录</el-button>
          <el-button type="info" @click="resetLoginFrom">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      loginFrom: {
        username: '1111',
        password: '1111'
      },
      loginRules: {
        username: [
          { required: true, message: '请输入姓名', trigger: 'blur' },
          { min: 3, max: 15, message: '长度在 3 到 15 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 8, max: 18, message: '长度在 8 到 18 个字符', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    resetLoginFrom () {
      this.$refs.loginFromRef.resetFields()
    },
    login () {
      this.$refs.loginFromRef.validate((valid) => {
        console.log(valid)
      })
    }
  }
}
</script>

<style lang="less" scoped>
  .login_box{
    height: 100%;
    width: 100%;
    background-color: skyblue;
  }

  .login_form{
    width: 450px;
    height: 300px;
    background-color: #ffffff;
    border-radius: 4px;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
  }

  .login_form .logo{
    position: absolute;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #ffffff;
    width: 140px;
    height: 140px;
    border-radius: 50%;
    padding: 10px;
    box-shadow: 0 0 10px #add;
    img {
      width: 100%;
      height: 100%;
      border-radius: 50%;
    }
  }

  .login_form .el-form{
    position: absolute;
    bottom: 0;
    padding: 0 20px;
    width: 100%;
    box-sizing: border-box;
  }

  .btns{
    display: flex;
    justify-content: flex-end;
  }
</style>
