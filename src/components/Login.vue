<template>
  <div class="login">
    <h1>Welcome to Use Contacts App!</h1>
    <el-row>
      <el-col :span="12" :offset="6">
        <el-carousel height="450px" type="card" arrow="never" :autoplay="false">
      <el-carousel-item>
        <el-card class="login-card">
          <el-form :model="loginForm" ref="form1" :rules="rules">
            <el-form-item label="username" prop="username">
              <el-input v-model="loginForm.username" name="username" type="text"></el-input>
            </el-form-item>

            <el-form-item label="password" prop="password">
              <el-input v-model="loginForm.password" name="password" type="password"></el-input>
            </el-form-item>
            <el-button type="primary" @click="login">login</el-button>
          </el-form>
        </el-card>
      </el-carousel-item>
        
      <el-carousel-item>
        <el-card class="box-card register-card">
          <el-form :model="registerForm" ref="form2" :rules="rules">
            <el-form-item label="username" prop="username">
              <el-input v-model="registerForm.username" name="username" type="text"></el-input>
            </el-form-item>
            <el-form-item label="password" prop="password">
              <el-input v-model="registerForm.password" name="password" type="password" required></el-input>
            </el-form-item>
            <el-button type="success" @click="register">Register</el-button>
          </el-form>
        </el-card>
      </el-carousel-item>
    </el-carousel>
      </el-col>
    </el-row>
  </div>
</template>

<script>
var axios = require('axios');
export default {
  name: 'Login',
  data () {
    return {
      loginForm: {
        username: "",
        password: ""
      },
      registerForm: {
        username: "",
        password: ""
      },
      rules: {
          username: [
            { required: true, message: 'Please input username', trigger: 'blur' },
            { type: 'string', min: 3, max: 8, message: 'keep length between 3 and 8', trigger: 'blur' }
          ],
          password: [
            { required: true, message: 'Please input your password', trigger: 'blur'}
          ]
        }
    }
  },
  methods: {
    login() {
      this.$refs['form1'].validate(valid => {
          if (valid) {
           axios.post('/api/login/', this.loginForm).then(res => {
            let data = res.data
            console.log(data)
            if(data.status !== '0') {
              this.$message.error(data.msg)
            } else {
              this.$message('welcome back, '+this.loginForm.username);
              this.$emit('loading', true);
              setTimeout(()=>{
              this.$router.push('/contacts');
              },500)
            }
          }).catch((error) => {
              console.log(error)
              this.$message('some thing happend');
          }) 
          } else {
            this.$message.warning('please finish your form first!')
          }
        });
    },
    register() {
      this.$refs['form2'].validate(valid => {
        if(valid) {
          axios.post('/api/register/', this.registerForm).then(res => {
          let data = res.data
          console.log(data);
            if(data.status !== '0') {
              console.log(data.msg)
              this.$message.error(data.msg);
            } else {
              this.$message.success('register success');
              Object.assign(this.loginForm, this.registerForm)
              this.login()
            }
        }).catch((error) => {
            console.log(error)
            this.$message.error('some thing happend');
        })
        } else {
            this.$message.warning('please finish your form first!')
        }
      })
    }
  },
  created() {
    this.$emit('loading', true);
    this.$cookie.set('test', 'Hello world!', 1);
    let sessionid = this.$cookie.get('sessionid');
    console.log(sessionid);
    console.log(this.$cookie.get('test'))
    if(sessionid != null){
      axios.get('api/login/').then(res => {
        console.log(res.data.status)
        if(res.data.status == '0') {
          this.$router.push('/contacts');
          this.$message.success('auto login');
        } else {
          this.$emit('loading', false);
        }
      })
    } else {
      this.$emit('loading', false);
    }
  }
}
</script>

<style scoped>

body {
  font-family: "Helvetica Neue",Helvetica,"PingFang SC","Hiragino Sans GB"
  ,"Microsoft YaHei","微软雅黑",Arial,sans-serif;
}

h1 {
  font-size: 40px
}
.login {
  margin-top: 10%;
}

.register {
  margin-top: 10%;
}

</style>
