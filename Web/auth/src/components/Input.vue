<template>
  <!--  Account Login -->
  <div class="login_box">
    <div class="login_panel">
      <div class="login_title">
        <img src="../assets/img/logo.png" alt="">
      </div>
        <router-link to="/auth">
        <div class="login_close">
        </div>
        </router-link>
      <div class="login_input">
      <input v-model="userId" type="tel" pattern="^\d{11}$" title="Input Email" placeholder="Email Address">
      <input v-model="userPassword" type="password" title="Input Password" placeholder="Password">
      <input class="bt" @click="login" type="submit" value="Login">
      </div>
      <div class= "signup">
        <label class="register_title">Need an account?</label>
        <router-link to="/register">
        <div class="button">
        <div class="signup_button">SIGN UP NOW</div>
        </div>
        </router-link>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Input',
  data () {
    return {
      userId: '',
      userPassword: '',
      userInfoApi: 'http://localhost/login' // 通过用户ID登录接口
    }
  },
  methods: {
    login () {
      this.$ajax({
        method: 'post',
        url: this.userInfoApi,
        data: this.qs.stringify({
          userId: this.userId,
          userPassword: this.userPassword
        })
      }).then((response) => {
        // 获取用户信息，登录成功
        if (response.data.data.userId !== 0) {
          this.$message({
            message: 'Login Success!',
            type: 'success'
          })
        } else {
          this.$message.error('Login Failed, Please Check Account Or Password!')
        }
        console.log(response.data.data)
      }).catch((error) => {
        this.$message.error('Login Failed, Please Check Account Or Password!')
        console.log(error)
      })
    }
  }
}
</script>

<style scoped>

  /*登录框*/
  .login_box {
    z-index: 99;
    position: absolute;
    width: 350px;
    height: 540px;
    top: 50%;
    left: 50%;
    margin-left: -190px;
    margin-top: -270px;
    background-color: #333333;
  }

  .login_close {
    position: absolute;
    top: 0px;
    right: 0px;
    width: 187.5px;
    height: 51px;
    background: url(../assets/img/qrcode.png) no-repeat right top;
    background-size: 100% 100%;
    border-top-right-radius: 5px;
    cursor: pointer;
    z-index: 99;
  }

  /*登录*/
  .login_panel {
    position: relative;
    display: block;
    top: 50%;
    left: 50%;
    margin: auto;
    width: 375px;
    height: 667px;
    padding: 0 0px;
    background-color: #333333;
    transform: translate(-50%, -50%);
    /* background: #fff; */
    border-radius: 5px;
    overflow: hidden;
  }

  .login_input{
    position: relative; top: 120px;
  }
  input[placeholder] {
    width: 147px;
    height: 24px;
    font-family: lato;
    font-size: 16px;
    font-weight: 500;
    font-stretch: normal;
    font-style: normal;
    line-height: normal;
    letter-spacing: -0.21px;
    text-align: center;
    color: #b2b2b2;
  }

  .signup{
    position: relative; top: 180px;
  }

  .signup .signup_button{
    display: block;
    margin: auto;
    margin-top: 13.5px;
    text-align: center;
    height: 14px;
    font-family: Lato;
    font-size: 12px;
    font-weight: 900;
    font-stretch: normal;
    font-style: normal;
    line-height: 1;
    letter-spacing: 1px;
    color: #ffffff;
  }
  .login_panel .login_title {
    text-align: center;
  }
  .signup .button{
    margin: auto;
    display: block;
    width: 345px;
    height: 44px;
    border: solid 2.5px #444444;
  }
  .login_panel .login_title img {
    margin-top: 140px;
    height: 21.5px;
    width: 251px;
    padding: 10px;
  }

  .login_panel .login_title p {
    margin-top: 15px;
    color: #999999;
    font-size: 15px;
  }

  .login_panel label {
    margin: auto;
    margin-bottom: 17px;
    display: block;
    text-align: center;
    width: 120px;
    height: 14px;
    font-family: lato;
    font-size: 12px;
    font-weight: normal;
    font-stretch: normal;
    font-style: normal;
    line-height: 1;
    letter-spacing: 0.2px;
    color: #b2b2b2;
  }

  .login_panel input {
    display: block;
    margin: 17px auto;
    width: 227.5px;
    height: 44px;
    padding: 0 0;
    line-height: 44px;
    font-size: 14px;
    color: #b2b2b2;
    outline: 0;
    border: 0;
    background: #444444;
  }
  /* 按钮 */
  .login_panel .bt {
    margin-top: 17px;
    width: 227.5px;
    height: 44px;
    color: #333333;
    background: #fbd000;
    cursor: pointer;
  }

  .login_panel .bt:hover {
    background-color: #cda456;
  }

</style>
