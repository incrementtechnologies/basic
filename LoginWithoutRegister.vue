<template>
  <div class="login-container">
    <div class="login-wrapper col-12 col-sm-12 col-lg-8 mx-auto">
      <div class="login-header">
        <img :src="require('src/assets/img/logo.png')" v-on:click="redirect('/')" />
      </div>
      <div class="login-message-holder login-spacer text-center" v-if="errorMessage != null">
        <span class="text-danger text-center"><b>Oops!</b> {{errorMessage}}</span>
      </div>
      <span style="width: 100%; float: left; text-align: center; font-size: 20px; margin-bottom: 65px; margin-top: 20px;">
        <b class="text-white h1">{{common.APP_ADDRESS}}</b>
      </span>
      <div class="col-sm-4 col-md-4 col-xs-4 col-lg-4 mx-auto">
        <div class="input-group login-spacer mb-4">
          <!-- <span class="input-group-addon" id="addon-1"><i class="fa fa-user"></i></span> -->
          <div class="input-group">
            <input type="text" class="form-control form-control-login" placeholder="Usercode" aria-describedby="addon-1" v-model="username">
          </div>
        </div>
        <div class="input-group login-spacer mt-4 mb-4">
          <!-- <span class="input-group-addon" id="addon-2"><i class="fa fa-key"></i></span> -->
          <div class="input-group">
            <input class="form-control form-control-login" style="border-right-style: none;" :type="visibility" placeholder="********" aria-describedby="addon-2" v-model="password" @keyup.enter="logIn()">
            <span style="background: none;" class="input-group-addon password">
              <i v-if="visibility == 'password'" @click="showPassword()" class="fa fa-eye" aria-hidden="true"></i>
              <i v-if="visibility == 'text'" @click="hidePassword()" class="fa fa-eye-slash" aria-hidden="true"></i>
            </span>
          </div>
        </div>
        <div class="input-group login-spacer mt-4 mb-4">
          <button class="btn dropdown-toggle form-control select-btn" type="button" id="dropdownMenuLink" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
            {{selected ? selected.address : 'Select location'}}
          </button>
          <div class="dropdown-menu btn-block ">
            <b class="dropdown-item font-weight-normal select-item" href="#" v-for="(el, ndx) in locations" :key="String(ndx) + String(el.id)" @click="selectItem(el)">{{el.address}}</b>
          </div>
        </div>
        <button class="btn btn-block login-spacer bg-white text-primary login-btn mt-4" v-on:click="logIn()">LOG IN</button>

        <!-- <button class="btn btn-warning btn-block login-spacer" v-on:click="redirect('/request_reset_password')">Forgot your Password?</button> -->
        <br>
        <!-- <div class="container-fluid separator">
            or
        </div>
        <br>
        <button class="btn btn-secondary btn-block login-spacer" v-on:click="redirect('/signup')">Create Account Now!</button> -->

<!--         <button class="btn btn-secondary btn-block login-spacer" v-on:click="redirect('/paymentConfirmation/kennettecanales@gmail.com/BWGFK8XY74RM1C2LZVEITPAJQ9D65N03/U4381TYWQKLIGS79DJNME5AVBRZPFH2O')">Payment Confirmation</button> -->
        <!-- <button class="btn btn-secondary btn-block login-spacer" v-on:click="redirect('/login_verification/magalso12/50DBXMFRGU6WI8VC3NA1OHLP7YTE92JS')">Verification</button> -->
      </div>
    </div>
    <authenticate-otp ref="authenticateOTP"></authenticate-otp>
  </div>
</template>
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
      visibility: 'password',
      locations: [
        {
          id: 1,
          address: `8 Queen's Road W`
        },
        {
          id: 2,
          address: `Location 2`
        }
      ],
      selected: null
    }
  },
  components: {
    'authenticate-otp': require('components/increment/generic/otp/Otp.vue')
  },
  methods: {
    selectItem(item) {
      this.selected = item
    },
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
          console.log(typeof response === 'string')
          if(typeof response === 'string'){
            $('#loading').css({'display': 'none'})
            this.errorMessage = response
          }else{
            AUTH.tokenData.loading = true
          }
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
<style scoped lang="scss">
@import "~assets/style/colors.scss";
.select-item {
  cursor: pointer;
}
.select-btn {
  font: 13px sans-serif;
  padding: .5em 1em;
  padding-top: .5em;
  padding-bottom: .5em;
  padding-left: 1em;
  padding-right: 1.6em;
  display: flex;
  justify-content: space-between;
  color: #16181a !important;
  background: $white !important;
  outline: none;
  border-top-right-radius: 3px !important;
  border-bottom-right-radius: 3px !important;
}

.select-btn::after {
  display: inline-block;
  width: 10px;
  height: 10px;
  margin: -.3em 0 0 .4em;
  vertical-align: middle;
  content: "";
  border: .3em solid;
  border-width: 0 .15em .15em 0;
  transform: rotateZ(45deg);
  font-weight: 100 !important;
}
.login-btn {
  color: $darkPrimary !important;
  font-weight: bold;
  font-size: 24px;
}
.login-container {
  height: 100vh!important;
  width:  100vw !important;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: $primary;
}
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
  height: 250px;
  color: #006600;
  width: 100%;
  float: left;
  text-align: center;
}

.login-header img{
  height: 250px !important;
  width: auto !important;
}

.login-header img:hover{
  cursor: pointer;
}

.login-message-holder{
  font-size: 12px;
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
  outline: $white;
  border: 1px solid $white;
  background: transparent !important;
  color: $white;
}
.form-control-login::-webkit-input-placeholder { /* Edge */
  color: $white;
}

.form-control-login:-ms-input-placeholder { /* Internet Explorer 10-11 */
  color: $white;
}

.form-control-login::placeholder {
  color: $white;
}
.password {
  border-top: 1px solid $white;
  border-right: 1px solid $white;
  border-bottom: 1px solid $white;
  color: $white;
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
