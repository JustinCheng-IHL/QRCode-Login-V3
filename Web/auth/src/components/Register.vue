<template>
  <div class="register_box">
    <div class="register_panel">
      <router-link to="/input">
        <div class="login_close"></div>
      </router-link>
      <el-steps :space="200" :active="0">
        <el-step></el-step>
        <el-step></el-step>
        <el-step></el-step>
        <el-step></el-step>
        <el-step></el-step>
      </el-steps>
      <div class="register_title">
        <img src="../assets/img/logo.png" alt="">
      </div>
      <!--      Step 1: Identity-->
      <div v-if="active===0">
        <label class="message-tag1">Create a display name for your iPersona Account, this will be the name everyone else can see.</label>
        <input v-model="userId" type="text" placeholder="Display Name" pattern="^\d{11}$" title="Please Enter Identity" required>
      </div>
      <!--      Step 2 : Email-->
      <div v-if="active===1">
        <label class="message-tag1">Please link a email to your iPersona account.</label>
        <input v-model="userEmail" type="tel" placeholder="Your Email Address" pattern="^\d{11}$" title="Please Enter Email" required>
      </div>

      <!--      Step 3 : Email Verification-->
      <div v-if="active===2">
<!--        <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">-->
<!--          <el-form-item label="邮箱" prop="email">-->
<!--            <el-input v-model="ruleForm.email" class="email" placeholder="邮箱"></el-input>-->
<!--          </el-form-item>-->
<!--          <el-form-item label="验证码" prop="code" class="pr">-->
<!--            <el-input prop="code" v-model="ruleForm.code" placeholder="验证码"></el-input>-->
<!--            <button @click="getCode()" class="code-btn" :disabled="!show">-->
<!--              <span v-show="show">发送验证码</span>-->
<!--              <span v-show="!show" class="count">{{count}} s</span>-->
<!--            </button>-->
<!--          </el-form-item>-->
<!--        </el-form>-->

        <!-- <label style="margin-top: 10px">Email：</label>
        <input v-model="userEmail" type="tel" pattern="^\d{11}$" title="Please Enter Email" required>
        <input class="bt" @click="getCode" type="submit" value="Get Token" style="margin-top: 10px"> -->
        <label class="message-tag1">We have send you a 6 digit code to verify your email address. Please input your digits below. </label>
        <input v-model="code" type="tel" placeholder="Your 6-digit code" pattern="^\d{11}$" title="Please Enter Token" required>
        <input class="bt" @click="verifyToken" type="submit" value="Verify" style="margin-top: 10px">

      </div>

      <!--      Step 4 : Password-->
      <div v-if="active===3">
        <label>Password：</label>
        <div class="input_container">
          <ul>
            <li v-bind:class="{ is_valid: contains_eight_characters }">8 Characters</li>
            <li v-bind:class="{ is_valid: contains_uppercase }">Contains Uppercase & Lowercase</li>
            <li v-bind:class="{ is_valid: contains_number }">Contains Number</li>
            <li v-bind:class="{ is_valid: contains_special_character }">Contains Special Character</li>
          </ul>
          <div class="checkmark_container" v-bind:class="{ show_checkmark: valid_password }">
            <svg width="50%" height="50%" viewBox="0 0 140 100">
              <path class="checkmark" v-bind:class="{ checked: valid_password }" d="M10,50 l25,40 l95,-70" />
            </svg>
          </div>
          <password v-model="userPassword" type="password" aria-placeholder="Your Password" class="password_first_input" @input="checkPassword"  title="Please Enter Password" autocomplete="off" :secureLength="secureLength" required></password>
        </div>
      </div>

      <!--      Step 5 : Confirm Password-->
      <div v-if="active===4">
        <label class="message-tag1">Please confirm your password</label>
        <input v-model="passwordConfirm" type="password" title="Please Confirm Password" required>
        <input class="bt" @click="addUser" type="submit" value="Register">
      </div>

      <el-button class="nextbt" @click="next" v-if="active<4"></el-button>

    </div>
  </div>
</template>

<script>
import Password from 'vue-password-strength-meter'

export default {
  name: 'Register',
  data () {
    return {
      active: 0,
      userId: '',
      userEmail: '',
      code: '',
      correctToken: '',
      userPassword: '',
      password_length: 0,
      contains_eight_characters: false,
      contains_number: false,
      contains_uppercase: false,
      contains_special_character: false,
      valid_password: false,
      passwordConfirm: '',
      secureLength: 8,
      userInfoApi: 'http://localhost/register',
      emailVerifyApi: 'http://localhost/mail',
      reg: /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
    }
  },
  components: {
    Password
  },
  methods: {
    checkPassword () {
      this.password_length = this.userPassword.length
      const format = /[$&+,:;=?@#|'<>.^*()%!-]/
      if (this.password_length >= 8) {
        this.contains_eight_characters = true
      } else {
        this.contains_eight_characters = false
      }
      this.contains_number = /\d/.test(this.userPassword)
      this.contains_uppercase = /[A-Z]/.test(this.userPassword) && /[a-z]/.test(this.userPassword)
      this.contains_special_character = format.test(this.userPassword)
      if (this.contains_eight_characters === true &&
        this.contains_special_character === true &&
        this.contains_uppercase === true &&
        this.contains_number === true) {
        this.valid_password = true
      } else {
        this.valid_password = false
      }
    },
    next () {
      if (this.active++ > 4) {
        this.active = 0
      }
    },
    getCode () {
      this.$ajax({
        method: 'post',
        url: this.emailVerifyApi,
        data: this.qs.stringify({
          userEmail: this.userEmail
        })
      }).then((response) => {
        if (response.data.code === 400) {
          this.$message.error('Email Send failed')
        } else if (response.data.code === 200) {
          console.log('Check Code is : ' + response.data.checkCode)
          this.correctToken = response.data.checkCode
          this.$message({
            message: 'Email send Success!',
            type: 'success'
          })
        } else {
          this.$message.error('Something Wrong.')
        }
        // console.log(response.data.checkCode)
      }).catch((error) => {
        this.$message.error('Error')
        console.log(error)
      })
    },
    verifyToken () {
      if (this.code === this.correctToken) {
        this.$message({
          message: 'Email Verified!',
          type: 'success'
        })
      } else {
        this.$message.error('Please Enter Correct Token.')
      }
    },
    addUser () {
      if (this.userId == null || this.userId === '') {
        this.$message.error('Identity is required!')
        return
      }
      if (this.userEmail == null || this.userEmail === '') {
        this.$message.error('userEmail is required!')
        return
      } else if (!this.reg.test(this.userEmail)) {
        this.$message.error('Please enter valid email address!')
        return
      }
      if (this.userPassword == null || this.userPassword === '') {
        this.$message.error('Password is required!')
        return
      }
      if (this.userPassword !== this.passwordConfirm) {
        this.$message.error('Password is NOT matched!')
        return
      }
      if (this.valid_password === false) {
        this.$message.error('Password is NOT valid!')
        return
      }
      this.$ajax({
        method: 'post',
        url: this.userInfoApi,
        data: {
          user_id: this.userId,
          user_password: this.userPassword,
          user_email: this.userEmail
        }
      }).then((response) => {
        if (response.data.code === 202) {
          this.$message.error('UserName already exits! Please change another identity.')
        } else if (response.data.data.userId !== 0) {
          this.$message({
            message: 'Register Success!',
            type: 'success'
          })
        } else {
          this.$message.error('Register Failed, Please Check Account Or Password!')
        }
        console.log(response.data.data)
      }).catch((error) => {
        this.$message.error('Register Failed, Please Check Account Or Password!')
        console.log(error)
      })
    }
  }
}
</script>

<style scoped>
  /*登录框*/
  .register_box {
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
    width: 51px;
    height: 51px;
    background: url(../assets/img/pcinput2.png) no-repeat right top;
    background-size: 100% 100%;
    border-top-right-radius: 5px;
    cursor: pointer;
    z-index: 99;
  }
  /*登录*/
  .register_panel {
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

  .Oval {
  width: 8px;
  height: 8px;
  border: solid 0.5px #fbd000;
  background-color: #fbd000;
  }

  .register_panel .register_title {
    text-align: center;
  }

  .register_panel .register_title img {
    margin-top: 90px;
    height: 21.5px;
    width: 251px;
    padding: 10px;
  }

  .register_panel .register_title p {
    color: #999999;
    font-size: 15px;
  }

  .register_panel label {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    font-size: 16px;
    font-weight: 500;
    font-stretch: normal;
    font-style: normal;
    line-height: normal;
    letter-spacing: -0.21px;
    text-align: center;
    color: #b2b2b2;
  }

  .register_panel input {
    display: block;
    margin-top: 55.5px;
    margin: auto;
    padding: 0 0;
    line-height: 42px;
    font-size: 1em;
    color: #b2b2b2;
    outline: 0;
    border: 0;
    width: 345px;
    height: 44px;
    text-align: center;
    background-color: #444444;
  }

  /* 按钮 */
  .register_panel .bt {
    margin-top: 35px;
    width: 120px;
    height: 40px;
    color: #444444  ;
    background: #fbd000;
    cursor: pointer;
  }

  .register_panel .nextbt {
    width: 60px;
    height: 60px;
    background: url(../assets/img/Next.png) center no-repeat;
    display:block;
    margin:auto;
    margin-top: 128.5px;
    -webkit-background-size: contain;
    -moz-background-size: contain;
    -o-background-size: contain;
    background-size: contain;
    border: 0;
  }

  .password_first_input {
    display: block;
    margin-top: 55.5px;
    margin: auto;
    padding: 0 0;
    line-height: 42px;
    font-size: 20px;
    color: #b2b2b2;
    outline: 0;
    border: 0;
    width: 345px;
    height: 44px;
    text-align: center;
    background-color: #444444;
  }
  .register_panel .bt:hover {
    background-color: #faed4b;
  }

  .message-tag1 {
    display: block;
    margin: auto;
    margin-top: 55.5px;
    width: 255px;
    height: 120px;
  }
  ul {
    padding-left: 20px;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
  }

  li {
    margin-bottom: 8px;
    color: #a9a8a5;
    font-size: 12px;
    position: relative;
  }

  li:before {
    content: "";
    width: 0%; height: 2px;
    background: #2ecc71;
    position: absolute;
    left: 0; top: 50%;
    display: block;
    transition: all .6s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  }

  .input_container {
    position: relative;
    padding: 3px;
    border-radius: 6px;
    background: #444444;
  }

  input[type="password"]:focus {
    border-color: rgba(50, 151, 211, .45);
  }

  .is_valid { color: rgba(136, 152, 170, 0.8); }
  .is_valid:before { width: 100%; }

  .checkmark_container {
    border-radius: 50%;
    position: absolute;
    top: -15px; right: -15px;
    background: #2ecc71;
    width: 50px; height: 50px;
    visibility: hidden;
    opacity: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    transition: opacity .4s ease;
  }

  .show_checkmark {
    visibility: visible;
    opacity: 1;
  }

  .checkmark {
    width: 100%;
    height: 100%;
    fill: none;
    stroke: white;
    stroke-width: 15;
    stroke-linecap: round;
    stroke-dasharray: 180;
    stroke-dashoffset: 180;
  }

  .checked { animation: draw 0.5s ease forwards; }

  @keyframes draw {
    to { stroke-dashoffset: 0; }
  }

</style>
