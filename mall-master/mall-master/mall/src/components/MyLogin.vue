<template>
  <div id="myLogin">
    <el-dialog title="登录" width="300px" center :visible.sync="isLogin">
      <el-form :model="LoginUser" :rules="rules" status-icon ref="ruleForm" class="demo-ruleForm">
        <el-form-item prop="name">
          <el-input prefix-icon="el-icon-user-solid" placeholder="请输入账号" v-model="LoginUser.name"></el-input>
        </el-form-item>
        <el-form-item prop="pass">
          <el-input prefix-icon="el-icon-view" type="password" placeholder="请输入密码" v-model="LoginUser.pass"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button size="medium" type="primary" @click="Login" style="width:100%;">登录</el-button>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>
<script>
import { mapActions } from 'vuex'

export default {
  name: 'MyLogin',
  data() {
    // 用户名的校验方法
    let validateName = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('请输入用户名'))
      }
      // 用户名以字母开头,长度在5-16之间,允许字母数字下划线
      const userNameRule = /^[a-zA-Z][a-zA-Z0-9_]{4,15}$/
      if (userNameRule.test(value)) {
        this.$refs.ruleForm.validateField('checkPass')
        return callback()
      } else {
        return callback(new Error('字母开头,长度5-16之间,允许字母数字下划线'))
      }
    }
    // 密码的校验方法
    let validatePass = (rule, value, callback) => {
      if (value === '') {
        return callback(new Error('请输入密码'))
      }
      // 密码以字母开头,长度在6-18之间,允许字母数字和下划线
      const passwordRule = /^[a-zA-Z]\w{5,17}$/
      if (passwordRule.test(value)) {
        this.$refs.ruleForm.validateField('checkPass')
        return callback()
      } else {
        return callback(new Error('字母开头,长度6-18之间,允许字母数字和下划线'))
      }
    }
    return {
      LoginUser: {
        name: '',
        pass: ''
      },
      // 用户信息校验规则,validator(校验方法),trigger(触发方式),blur为在组件 Input 失去焦点时触发
      rules: {
        name: [{ validator: validateName, trigger: 'blur' }],
        pass: [{ validator: validatePass, trigger: 'blur' }]
      }
    }
  },
  computed: {
    // 获取vuex中的showLogin，控制登录组件是否显示
    isLogin: {
      get() {
        return this.$store.getters.getShowLogin
      },
      set(val) {
        this.$refs['ruleForm'].resetFields()
        this.setShowLogin(val)
      }
    }
  },
  methods: {
    ...mapActions(['setUser', 'setShowLogin']),
    Login() {
      // 通过element自定义表单校验规则，校验用户输入的用户信息
      this.$refs['ruleForm'].validate(async valid => {
        //如果通过校验开始登录
        if (valid) {
          let { data } = await this.$axios.post('/api/userlogin', {
            username: this.LoginUser.name,
            password: this.LoginUser.pass
          })
          // console.log(data)
          if (data.code == '200') {
            this.$message.success('登录成功!')
            this.isLogin = false
            sessionStorage.setItem('user', JSON.stringify(data.data))
            // console.log(data.data)
            this.$store.dispatch('setUser', data.data)
            // console.log(this.$store.state)
          } else {
            this.$message.error('用户名或密码错误!')
            this.$refs['ruleForm'].resetFields()
          }
        } else {
          return false
        }
      })
    }
  }
}
</script>
<style>
</style>