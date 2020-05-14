<template>
  <div class="row">
    <div class="col-md-6 col-lg-4 mx-auto signup-container">
      <div class="signup-wrapper">
        <div class="signup-header" style="margin-top: 50px;">
          <img src="../../../assets/img/logo.png" v-on:click="redirect('/')">
        </div>
        <span style="width:100%;float:left;text-align:center;font-size:20px;margin-bottom:20px;">
          Register to <b class="text-primary">{{common.APP_NAME}}</b>
        </span>
        <div class="signup-holder">
          <div class="login-message-holder login-spacer text-center" v-if="errorMessage != ''">
            <span class="text-danger text-center"><b>Oops!</b> {{errorMessage}}</span>
          </div>
          <div>
            <div class="input-group login-spacer">
              <span class="input-group-addon" id="addon-1"><i class="fa fa-user"></i></span>
              <input type="text" class="form-control form-control-login" placeholder="Username" aria-describedby="addon-1" v-model="username">
            </div>
            <div class="input-group login-spacer">
              <span class="input-group-addon" id="addon-1"><i class="fa fa-envelope"></i></span>
              <input type="text" class="form-control form-control-login" placeholder="Email" aria-describedby="addon-1" v-model="email">
            </div>
            <div class="input-group login-spacer">
              <span class="input-group-addon" id="addon-2"><i class="fa fa-key"></i></span>
              <input type="password" class="form-control form-control-login" placeholder="Password" aria-describedby="addon-2" v-model="password">
            </div>
            <div class="input-group login-spacer">
              <span class="input-group-addon" id="addon-2"><i class="fa fa-key"></i></span>
              <input type="password" class="form-control form-control-login" placeholder="Confirm Password" aria-describedby="addon-2" v-model="cpassword">
            </div>
            <button class="btn btn-primary btn-block login-spacer" v-on:click="signUp()">Signup</button>
            <div class="input-group login-spacer">
              <label>By signing up, you agree to our <b class="text-primary" @click="openModal('#termsAndConditionsModal')">Terms</b> and <b class="text-primary" @click="openModal('#privacyModal')">Privacy Policy</b></label>
            </div>
            <div class="input-group login-spacer" style="margin-top: 50px; border-top: solid 1px #ddd;">
              <label>Have an account? <b class="text-primary" @click="redirect('/login')">Login</b></label>
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>
<style scoped>


.signup-container{
  margin-top: 25px;
}

.signup-header{
  height: 100px;
  color: #006600;
  width: 100%;
  float: left;
  text-align: center;
}
.signup-header img{
  height: 100px !important;
  width: auto !important;
}

.signup-header img:hover{
  cursor: pointer;
}

.header-title{
  width: 90%;
  margin:  25px 5% 0 5%;
  font-size: 24px;
  font-weight: 700px;
}

.signup-holder{
  width: 90%;
  margin: 0 5% 0 5%;
  float: left;
}

.input-holder{
  width: 90%;
  margin:  0 5% 0 5%;
}

.form-control{
  height: 45px !important;
}
.btn{
  height: 50px !important;
}
.input-group{
  margin-top: 5px;
  margin-bottom: 5px;
}
.site-title{
  margin-top: 25px;
  width: 100%;
  float: left;
}
.site-title img{
  width: 50px;
  width: 50px;
  float: left;
  margin-right: 10px;
}
.site-title .app-name{
  float: left;
}

.account-type{
  width: 120px !important;
}

.options{
  width: 90%;
  margin:  0 5% 0 5%;
  float: left;
}
.options button{
  width: 49% !important;
  float: left !important;
  height: 60px !important;
}
.input-group label{
  width: 100%;
  float: left;
  line-height: 50px;
  text-align: center;
}

.input-group-addon{
  width: 15% !important;
  text-align: center !important;
  padding: 0px !important;
  display: block !important;
  line-height: 43px !important;
}

.input-group label b:hover{
  cursor: pointer;
}
/*-------------- Extra Small Screen for Mobile Phones --------------*/
@media (max-width: 991px){
  .custom-holder{
    box-shadow: 0 0 0 0 #fff !important;
    margin-top: 50px !important;
  }
}
</style>
<script>
import ROUTER from 'src/router'
import AUTH from 'src/services/auth'
import CONFIG from 'src/config.js'
import COMMON from 'src/common.js'
export default {
  mounted(){
    // this.getSchools()
  },
  data(){
    return {
      username: '',
      email: '',
      password: '',
      cpassword: '',
      type: 'USER',
      errorMessage: '',
      user: AUTH.user,
      tokenData: AUTH.tokenData,
      flag: false,
      schools: null,
      schoolIndex: null,
      config: CONFIG,
      common: COMMON
    }
  },
  methods: {
    signUp(){
      this.validate()
      let parameter = {
        username: this.username,
        email: this.email,
        password: this.password,
        config: CONFIG,
        account_type: this.type,
        referral_code: null,
        status: 'ADMIN'
      }
      if(this.flag === true){
        $('#loading').css({'display': 'block'})
        this.APIRequest('accounts/create', parameter).then(response => {
          $('#loading').css({'display': 'none'})
          if(response.error !== null){
            if(response.error.status === 100){
              let message = response.error.message
              if(typeof message.username !== undefined && typeof message.username !== 'undefined'){
                this.errorMessage = message.username[0]
              }else if(typeof message.email !== undefined && typeof message.email !== 'undefined'){
                this.errorMessage = message.email[0]
              }
            }else if(response.data !== null){
              if(response.data > 0){
                this.login()
              }
            }
          }
          // this.redirect('/verification/' + this.email)
          // this.login()
        })
      }
    },
    redirect(parameter){
      ROUTER.push(parameter)
    },
    validate(){
      this.errorMessage = null
      let reqWhiteSpace = /\s/
      if(reqWhiteSpace.test(this.username)){
        this.errorMessage = 'Username should not contain a space.'
        this.flag = false
      }else if(AUTH.validateEmail(this.email) === false){
        this.errorMessage = 'You have entered an invalid email address.'
        this.flag = false
      }else if(this.username.length < 6){
        this.errorMessage = 'Username must be atleast 6 characters.'
        this.flag = false
      }else if(this.password.length < COMMON.passwordLimit){
        this.errorMessage = 'Password must be atleast ' + COMMON.passwordLimit + ' characters.'
        this.flag = false
      }else if(/^[a-zA-Z0-9]+$/.test(this.password)){
        this.errorMessage = 'Password must be alphanumeric characters.'
        this.flag = false
      }else if(this.password.localeCompare(this.cpassword) !== 0){
        this.errorMessage = 'Password did not match.'
      }else if(this.username.length >= 6 && this.email !== null && this.password !== null && this.password.length >= 6 && this.password.localeCompare(this.cpassword) === 0 && this.type !== null && AUTH.validateEmail(this.email) === true){
        this.errorMessage = null
        this.flag = true
      }else{
        this.errorMessage = 'Please fill in all required fields.'
        this.flag = false
      }
    },
    login(){
      AUTH.authenticate(this.username, this.password, (response) => {
        ROUTER.push('dashboard')
      }, (response, status) => {
        $('#loading').css({'display': 'none'})
        if(status === 401){
          this.errorMessage = 'Username and Password did not match.'
        }else if(status === 402){
          this.errorMessage = response.error
        }else{
          this.errorMessage = 'Cannot log in? Contact us through email: ' + this.common.APP_EMAIL
        }
      })
    },
    openModal(id){
      $(id).modal('show')
    }
  }
}
</script>
