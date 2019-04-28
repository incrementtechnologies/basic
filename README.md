# basic

# config
Declare Variable 
```javascript
  APP_NAME: '',
  APP_EMAIL: '',
  USER_TYPE: [{
    title: 'USER'
  }, {
    title: 'ADMIN'
  }]
```
# routes

```javascript
  {
    path: '/signup'
  },
  {
    path: '/signup_partner'
  },
  {
    path: '/verification/:email'
  },
  {
    path: '/login_verification/:username/:code'
  },
  {
    path: '/login'
  },
  {
    path: '/request_reset_password'
  },
  {
    path: '/reset_password/:username/:code'
  },
  {
    path: '/refer_register/:email/:code'
  },
```

# index.js

Declare Variable

``` javascript

  otpDataHolder: {
    userInfo: null,
    data: null
  }
```

Methods

``` javascript

  checkOtp(setting){
    if(setting !== null){
      if(parseInt(setting.email_otp) === 1 || parseInt(setting.sms_otp) === 1){
        // ask otp code here
        $('#otpModal').modal({
          backdrop: 'static',
          keyboard: true,
          show: true
        })
      }else{
        this.proceedToLogin()
      }
    }else{
      this.proceedToLogin()
    }
  },
  proceedToLogin(){
    let userInfo = this.otpDataHolder.userInfo
    let data = this.otpDataHolder.data
    let profile = data[0].account_profile
    let checkout = data[0].checkout
    let plan = data[0].plan
    let notifSetting = data[0].notification_settings
    this.setUser(userInfo.id, userInfo.username, userInfo.email, userInfo.account_type, userInfo.status, profile, checkout, plan, notifSetting)
    ROUTER.push('/templates')
  }
```
# Call checkOtp() during authentication