<template>
  <div class="col-sm-12 col-md-6 col-lg-4 mx-auto holder">
    <div class="login-wrapper">
      <div class="login-header" style="margin-top: 75px;">
        <img :src="require('src/assets/img/logo.png')" v-on:click="redirect('/')">
      </div>
      <span style="width:100%;float:left;text-align:center;font-size:20px;margin-bottom:20px;">
        Login to <b class="text-primary">{{common.APP_NAME}}</b>
      </span>
      <div class="login-message-holder login-spacer text-center" v-if="errorMessage != null">
        <p class="text-danger text-center"><b>Oops!</b> {{errorMessage}}</p>
      </div>
      <div>
        <div class="input-group login-spacer">
          <span class="input-group-addon" id="addon-1"><i class="fa fa-user"></i></span>
          <div class="input-group">
            <input type="text" class="form-control form-control-login" placeholder="Username or Email" aria-describedby="addon-1" v-model="username">
          </div>
        </div>
        <div class="input-group login-spacer">
          <span class="input-group-addon" id="addon-2"><i class="fa fa-key"></i></span>
          <div class="input-group">
            <input class="form-control form-control-login" style="border-right-style: none;" :type="visibility" placeholder="********" aria-describedby="addon-2" v-model="password" @keyup.enter="logIn()">
            <span style="background: white;" class="input-group-addon password">
              <i v-if="visibility == 'password'" @click="showPassword()" class="fa fa-eye" aria-hidden="true"></i>
              <i v-if="visibility == 'text'" @click="hidePassword()" class="fa fa-eye-slash" aria-hidden="true"></i>
            </span>
          </div>
        </div>
        <button class="btn btn-primary btn-block login-spacer" v-on:click="logIn()">Login</button>
        <button class="btn btn-warning btn-block login-spacer" v-if="!common.hideForgotPassword" v-on:click="redirect('/request_reset_password')">Forgot your Password?</button>
        <br>
        <div class="container-fluid separator">
            or
        </div>
        <br>
        <button class="btn btn-secondary btn-block login-spacer" v-on:click="redirect('/signup')">Create Account Now!</button>

<!--         <button class="btn btn-secondary btn-block login-spacer" v-on:click="redirect('/paymentConfirmation/kennettecanales@gmail.com/BWGFK8XY74RM1C2LZVEITPAJQ9D65N03/U4381TYWQKLIGS79DJNME5AVBRZPFH2O')">Payment Confirmation</button> -->
        <!-- <button class="btn btn-secondary btn-block login-spacer" v-on:click="redirect('/login_verification/magalso12/50DBXMFRGU6WI8VC3NA1OHLP7YTE92JS')">Verification</button> -->
      </div>
    </div>
    <authenticate-otp ref="authenticateOTP"></authenticate-otp>
  </div>
</template>
<style scoped lang="scss">
@import "~assets/style/colors.scss";
.holder{
  margin-top: 25px;
}

.bg-primary{
  background: $primary !important; 
}

.login-wrapper{
  width: 80%;
  margin: 0 10% 50px 10%;
}

.login-header{
  height: 100px;
  color: #006600;
  width: 100%;
  float: left;
  text-align: center;
}

.login-header img{
  height: 100px !important;
  width: auto !important;
}

.login-header img:hover{
  cursor: pointer;
}

.login-message-holder{
  min-height: 30px;
  font-size: 12px;
  float: left;
  width: 100%;
  overflow: hidden;
}

.login-spacer{
  margin-bottom: 10px;
}/*-- login-spacer --*/

.forgot-password a{
  color: #006600 !important;
}
.forgot-password a:hover{
  cursor: pointer !important;
  text-decoration: underline !important;
  color: #009900 !important;
}

.btn{
  height: 50px !important;
}

.input-group-addon{
  width: 15% !important;
  text-align: center !important;
  padding: 0px !important;
  display: block !important;
  line-height: 43px !important;
}

.form-control-login{
  height: 45px !important;
}

/*    Line with text on top  */
.separator>*{
  display: inline-block;
  vertical-align: middle;
}

.separator {
    text-align: center;
    border: 0;
    white-space: nowrap;
    display: block;
    overflow: hidden;
    padding: 0;
    margin: 0;
}

.separator:before, .separator:after {
    content: "";
    height: 1px;
    width: 50%;
    background-color: #ccc;
    margin: 0 5px 0 5px;
    display: inline-block;
    vertical-align: middle;
}

.separator:before {
    margin-left: -100%;
}

.separator:after {
    margin-right: -100%;
}

@media (max-width: 992px){
  .login-wrapper{
    width: 96%;
    margin: 0 2% 0 2%;
  }
}
</style>
<script>
import ROUTER from 'src/router'
import AUTH from 'src/services/auth'
import COMMON from 'src/common.js'
export default {
  mounted(){
  },
  data(){
    return {
      username: null,
      password: null,
      errorMessage: null,
      user: AUTH.user,
      tokenData: AUTH.tokenData,
      otpCode: null,
      otpErrorCode: null,
      common: COMMON,
      visibility: 'password'
    }
  },
  components: {
    'authenticate-otp': require('components/increment/generic/otp/Otp.vue')
  },
  methods: {
    showPassword() {
      this.visibility = 'text'
    },
    hidePassword() {
      this.visibility = 'password'
    },
    logIn(){
      if(this.username !== null && this.username !== '' && this.password !== null && this.password !== ''){
        $('#loading').css({'display': 'block'})
        AUTH.authenticate(this.username, this.password, (response) => {
          this.errorMessage = null
          $('#loading').css({'display': 'none'})
          AUTH.tokenData.loading = true
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
      }else{
        $('#loading').css({'display': 'none'})
        this.errorMessage = 'Please fill up all the required fields.'
      }
    },
    redirect(parameter){
      ROUTER.push(parameter)
    },
    request(parameter){
      this.APIRequest(parameter, {}).then(response => {
      })
    },
    cancelOTP(){
      AUTH.deaunthenticate()
      $('#authenticateOTP').modal('hide')
    },
    successOTP(){
      AUTH.proceedToLogin()
      $('#authenticateOTP').modal({
        backdrop: 'static',
        keyboard: false,
        show: false
      })
    }
  }
}
</script>
