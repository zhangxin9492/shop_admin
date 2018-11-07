<template>
  <div class="login_container">
    <el-form :model="ruleForm2" status-icon :rules="rules2" ref="ruleForm2" class="demo-ruleForm">
      <el-form-item label="用户名" prop="username">
        <el-input type="text" v-model="ruleForm2.username" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="pass">
        <el-input type="password" v-model="ruleForm2.pass" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm('ruleForm2')">提交</el-button>
        <el-button @click="resetForm('ruleForm2')">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data () {
    var validateUsername = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入用户名'))
      } else {
        callback()
      }
    }
    var validatePass = (rule, value, callback) => {
      if (value === '') {
        callback(new Error('请输入密码'))
      } else {
        callback()
      }
    }
    return {
      ruleForm2: {
        username: '',
        pass: ''
      },
      rules2: {
        username: [
          { validator: validateUsername, trigger: 'blur' },
          { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
        ],
        pass: [{ validator: validatePass, trigger: 'blur' }]
      },
      labelPosition: 'top'
    }
  },
  methods: {
    submitForm (formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          alert('submit!')
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    resetForm (formName) {
      this.$refs[formName].resetFields()
    }
  }
}
</script>

<style>
.login_container {
  width: 100%;
  height: 100%;
  background-color: #2d434c;
  overflow: hidden;
}
.demo-ruleForm {
  width: 400px;
  background-color: #fff;
  padding: 20px;
  border-radius: 10px;
  margin: 200px auto;
}
.el-form-item__label {
  display: inline-block;
  float: none;
  text-align: left;
}
.el-form-item__label::before {
  content: "*";
  color: #f56c6c;
  margin-right: 4px;
}
.el-form-item__content {
  margin: 0;
}
</style>
