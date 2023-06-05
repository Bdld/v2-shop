<template>
  <div class="login_container">
    <div class="login_box">
      <h2>登录后台管理系统</h2>
      <!-- 登录表单区域 -->
      <el-form  label-width="0px" class="login_form" :model="login_form" :rules="loginFormRule" ref="loginFormRef">
        <!-- 用户名 -->
        <el-form-item prop="username">
          <el-input prefix-icon="iconfont icon-user" v-model="login_form.username" ></el-input>
        </el-form-item>
        <!-- 密码 -->
        <el-form-item prop="password">
          <el-input prefix-icon="iconfont icon-3702mima" v-model="login_form.password" type="password" ></el-input>
        </el-form-item>
        <!-- 按钮 -->
        <el-form-item class="btns">
          <el-button type="primary" @click="login">登录</el-button>
          <el-button type="primary" @click="resetLoginForm">重置</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      // 登录表单的数据源绑定对象
      login_form: {
        username: 'admin',
        password: '123456'
      },
      // 登录表单的验证规则对象
      loginFormRule: {
        username: [
          { required: true, message: '请输入登录名称', trigger: 'blur' },
          { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入登录密码', trigger: 'blur' },
          { min: 6, max: 8, message: '长度在 6 到 8 个字符', trigger: 'blur' }
        ]

      }
    }
  },
  methods: {
    resetLoginForm () {
      this.$refs.loginFormRef.resetFields()
    },
    login () {
      // 给表单绑定ref，有了表单实例对象后就可以使用validate方法。第一个参数 valid 是个回调函数返回 预验证是否通过
      this.$refs.loginFormRef.validate(async valid => {
        if (!valid) return
        // 预验证通过了的话发起网络请求,$http是在main.js里配置了 给Vue原型绑定了axios，全局使用
        const { data: res } = await this.$http.post('login', this.login_form)
        // $messages是在插件element.js里配置了 给Vue原型绑定了Message,全局使用
        if (res.meta.status !== 200) return this.$message.error('登录失败')
        this.$message.success('登录成功')
        // 登录成功后，在sessionStorage中记录token。后续除了登录外的api，必须在登录之后才能访问
        window.sessionStorage.setItem('token', res.data.token)
        this.$router.push('/home')
      })
    }
  }
}
</script>

<style lang="less" scoped>
h2 {
  text-align:center;
  color: #2b4b6b;
}
.login_container{
  background-color: #2b4b6b;
  height: 100%;
}

.login_box{
  width: 450px;
  height: 300px;
  background-color: #fff;
  border-radius: 3px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}

.login_form{
  position: absolute;
  bottom: 5%;
  width: 100%;
  padding: 0 40px;
  box-sizing: border-box;
}

.btns {
  display: flex;
  justify-content: space-between;
}
</style>
