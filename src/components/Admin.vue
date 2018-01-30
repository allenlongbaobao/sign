<template>
  <div class="modal-mask">
    <div class="modal-wrapper">
      <div class="modal-container">
        <div class="modal-header">
          <el-button type="button" @click="toggleSignIn">登陆</el-button>
          <el-button type="button" @click="toggleSignUp">注册</el-button>
        </div>
        <div class="modal-body">
          <transition name="fade">
            <el-form class="signInForm" :model="signInForm" ref="signInForm" v-show="signInShow" status-icon label-width="100px" >
              <el-form-item label="用户名" prop="username" :rules="[{ required: true, message: '用户名不能为空'}, {min:6, max:12, message: '长度在6-12个字符之间'}]">
                <el-input type="username" v-model="signInForm.username"></el-input>
              </el-form-item>
              <el-form-item label="密码" prop="pass" :rules="[{required: true, message: '密码不能为空'}]">
                <el-input type="password" v-model="signInForm.pass" auto-complete="off"></el-input>
              </el-form-item>
            </el-form>
          </transition>
          <transition name="fade">
            <el-form class="signUpForm" :model="signUpForm" :rules="signUpRule" ref="signUpForm" v-show="signUpShow" label-width="100px">
              <el-form-item label="用户名" prop="username" :rules="[{required: true, message: '用户名不能为空'}, {min:6, max:12, message: '长度在6-12个字符之间'}]">
                <el-input type="text" v-model="signUpForm.username"></el-input>
              </el-form-item>
              <el-form-item label="邮箱" prop="email" :rules="[{required: true, message: '邮箱不能为空'}, {type: 'email', message: '请输入正确的邮箱格式'}]">
                <el-input type="text" v-model="signUpForm.email"></el-input>
              </el-form-item>
              <el-form-item label="密码" prop="pass">
                <el-input type="password" v-model="signUpForm.pass" auto-complete="off"></el-input>
              </el-form-item>
              <el-form-item label="确认密码" prop="checkPass">
                <el-input type="password" v-model="signUpForm.checkPass" auto-complete="off"></el-input>
              </el-form-item>
            </el-form>
          </transition>
        </div>
        <div class="modal-footer">
          <el-button id="signBtn" type="button" @click="sign">登陆</el-button>
          <el-button type="danger" @click="cancel">取消</el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// ip 地址，如果部署到服务器上，记得更改 
let IP = 'localhost'
export default {
  data () {
    let signInForm = {
      username: '',
      pass: ''
    }
    let signUpForm = {
      username: '',
      email: '',
      pass: '',
      checkPass: ''
    }
    let validatePass = function (rule, value, callback) {
      if (value === '') {
        callback(new Error('请输入密码'))
      } else {
        callback()
      }
    }

    let validateCheckPass = function (rule, value, callback) {
      if (value !== signUpForm.pass) {
        callback(new Error('两次输入密码不一致'))
      } else {
        callback()
      }
    }

    return {
      signUpShow: false,
      signInShow: true,
      signInForm,
      signUpForm,
      signUpRule: {
        pass: {validator: validatePass, trigger: 'blur'},
        checkPass: {validator: validateCheckPass, trigger: 'blur'}
      }
    }
  },
  components: {
  },
  computed: {
  },
  created () {
  },
  methods: {
    toggleSignIn: function () {
      this.signUpShow = false
      this.signInShow = true
      let btn = document.getElementById('signBtn')
      btn.innerHTML = '登陆'
    },
    toggleSignUp: function () {
      this.signInShow = false
      this.signUpShow = true
      let btn = document.getElementById('signBtn')
      console.log(btn)
      btn.innerHTML = '注册'
    },
    cancel: function () {
      this.$router.push({name: 'mainPage'})
    },
    sign: function () {
      if (this.signUpShow === true && this.signInShow === false) {
        this.$refs['signUpForm'].validate().then((value) => {
          this.signUp().then((data) => {
            this.$router.push({name: 'manage', params: {user: data}})
          }).catch(err => {
            alert('出现错误', err.message)
          })
        }).catch(() => {
          alert('error： 表单输入错误')
        })
      } else if (this.signUpShow === false && this.signInShow === true) {
        this.$refs['signInForm'].validate().then(() => {
          this.signIn().then((data) => {
            this.$router.push({name: 'manage', params: {user: data}})
          }).catch(err => {
            alert('出现错误', err.message)
          })
        }).catch(() => {
          alert('error: 用户名或者密码错误')
        })
      }
    },
    signUp: function () {
      let data = {
        username: this.signUpForm.username,
        password: this.signUpForm.pass,
        email: this.signUpForm.email,
        createAt: new Date()
      }
      return this.$http.post(IP + '/api/signUp', data, {
        withCredentials: true
      }).then(response => {
        return response.data
      }).catch(err => {
        throw new Error(err.message)
      })
    },
    signIn: function () {
      let data = {
        username: this.signInForm.username,
        password: this.signInForm.pass
      }
      return this.$http.post(IP + '/api/signIn', data, {
        withCredentials: true
      }).then(response => {
        return response.data
      }).catch(err => {
        throw new Error(err.message)
      })
    }
  }
}
</script>

<style scoped>
.modal-mask {
  position: fixed;
  z-index: 1000;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .5);
  display: table;
  transition: opacity .3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
  margin: 0px auto;
  margin-left: 0px;
  margin-right: 0px;
}

.modal-container {
  width: 30%;
  height: 400px;
  vertical-align: middle;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, .33);
  transition: all .3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal-header {
  width: 41%;
  margin: 0 auto;
}

.modal-header .el-button{
  margin-top: 0;
  color: #42b983;
  margin-left: 0px;
  margin-right: 0px;
}

.modal-body {
  margin: 20px 0;
  width: 100%;
  height: 60%;
}

.modal-footer {
  width: 50%;
  margin: 0 auto;
}

.modal-default-button {
  float: right;
}

.signInForm {
  vertical-align: middle;
}
/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.fade-enter-active, .fade-leave-active {
  transition: opacity ;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
