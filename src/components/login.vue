<template>
  <div class="login_container">
    <div class="login_box">
      <div class="avatar_box">
        <img src="../assets/logo.png" />
        <el-form
          ref="loginRef"
          label-width="0px"
          :model="loginForm"
          :rules="loginFormRlue"
          class="form_box"
        >
          <el-form-item prop="username">
            <el-input v-model="loginForm.username" prefix-icon="el-icon-user-solid"></el-input>
          </el-form-item>
          <el-form-item prop="password">
            <el-input v-model="loginForm.password" prefix-icon="el-icon-eleme" type="password"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="login">登陆</el-button>
            <el-button type="info" @click="resetlogin" style="float:right">重置</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script>
// import async from 'q'
export default {
  data () {
    return {
      loginForm: {
        username: 'admin',
        password: '123456'
      },
      loginFormRlue: {
        username: [
          { required: true, message: '请输入登录名称', trigger: 'blur' },
          { min: 3, max: 10, mseeage: '长度在3到10个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, max: 15, message: '长度在6到15个字符', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    resetlogin () {
      this.$refs.loginRef.resetFields()
    },
    login () {
      this.$refs.loginRef.validate(async valid => {
        if (!valid) {
          return 0
        }
        const { data: res } = await this.$http.post('login', this.loginForm)
        if (res.meta.status !== 200) return this.$message.error('登录失败')
        this.$message.success('登录成功')
        window.sessionStorage.setItem('token', res.data.token)
        this.$router.push('/home')
      })
    }
  }
}
</script>

<style lang="less" scoped>
.login_container {
  background-color: rgb(143, 8, 206);
  height: 100%;
}

.login_box {
  background-color: #fff;
  height: 300px;
  width: 450px;
  border-radius: 3px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);

  .avatar_box {
    height: 130px;
    width: 130px;
    border: 1px solid #eee;
    border-radius: 50%;
    padding: 10px;
    box-shadow: 0 0 10px #ddd;
    left: 50%;
    transform: translate(-50%, -50%);
    position: absolute;
    background-color: #fff;
    img {
      height: 100%;
      width: 100%;
      border-radius: 50%;
      background-color: #eee;
    }
    .form_box {
      position: relative;
      top: 15%;
      width: 300px;
      left: 50%;
      transform: translate(-50%);
    }
  }
}
</style>
