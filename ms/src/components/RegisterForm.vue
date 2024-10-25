<template>
  <div id="app" class="register-container">
    <!-- 标题与副标题 -->
    <h2 class="register-title">欢迎注册秒杀系统</h2>
    <p class="register-subtitle">每天，乐在抢购。</p>

    <!-- 注册表单 -->
    <el-form :model="form" ref="form" class="form-box">
      <!-- 用户名输入框 -->
      <el-form-item :error="usernameError">
        <el-input v-model="form.username" placeholder="昵称" style="width: 300px" @blur="checkUsername"></el-input>
      </el-form-item>

      <!-- 密码输入框 -->
      <el-form-item :error="passwordError">
        <el-input v-model="form.password" placeholder="密码" type="password" style="width: 300px" @blur="checkPassword"
          @input="validatePassword" @focus="showPasswordRules = true" show-password></el-input>
      </el-form-item>
      <!-- 密码规则提示 -->
      <div v-if="showPasswordRules" class="password-rules">
        <p>
          <span v-if="noSpacesValid" :class="{ valid: noSpacesValid }">✔</span> 不能包括空格
        </p>
        <p>
          <span v-if="lengthValid" :class="{ valid: lengthValid }">✔</span> 长度为8-16个字符
        </p>
        <p>
          <span v-if="complexityValid" :class="{ valid: complexityValid }">✔</span> 必须包含字母、数字、符号中至少2种
        </p>
      </div>
      <!-- 协议复选框 -->
      <el-form-item>
        <el-checkbox v-model="form.agree">
          我已阅读并同意
          <a href="#">服务协议</a> 和
          <a href="#">隐私政策</a>
        </el-checkbox>
      </el-form-item>

      <!-- 注册按钮 -->
      <el-form-item>
        <el-button type="primary" @click="submitForm" style="width: 300px">
          立即注册
        </el-button>
      </el-form-item>
    </el-form>

    <!-- 注册成功/失败信息 -->
    <el-dialog :visible.sync="dialogVisible" title="注册信息">
      <p>{{ submitMessage }}</p>
    </el-dialog>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  data() {
    return {
      form: {
        username: '',
        password: '',
        agree: false
      },
      usernameError: '',
      passwordError: '',
      dialogVisible: false,
      submitMessage: '',
      noSpacesValid: false,
      lengthValid: false,
      complexityValid: false,
      showPasswordRules: false
    };
  },
  methods: {
    validatePassword() {
      const password = this.form.password;

      // 检查是否包含空格
      this.noSpacesValid = !/\s/.test(password);

      // 检查长度是否为8-16字符
      this.lengthValid = password.length >= 8 && password.length <= 16;

      // 检查密码复杂度，是否包含至少2种类型（字母、数字、符号）
      const hasLetter = /[a-zA-Z]/.test(password);
      const hasNumber = /\d/.test(password);
      const hasSpecialChar = /[\W_]/.test(password);
      const validTypes = [hasLetter, hasNumber, hasSpecialChar].filter(Boolean).length;
      this.complexityValid = validTypes >= 2;
    },
    // 检查用户名
    checkUsername() {
      if (!this.form.username) {
        this.usernameError = '用户名不能为空';
      } else {
        this.usernameError = '';
      }
    },
    checkPassword() {
      this.showPasswordRules = false;
      if (!this.form.password) {
        this.passwordError = '密码不能为空';
      } else {
        this.passwordError = '';
      }
    },
    submitForm() {
      if (!this.form.username || !this.form.password || !this.form.agree) {
        this.submitMessage = '登录失败，请填写完整信息并同意服务协议。';
        this.dialogVisible = true;
      }

      const user = { name:this.form.username,password: this.form.password};
      axios.post('http://localhost:28080/users', user)
        .then(response => {
          if (response.data.code === 200) {
            //判断注册是否成功
            if(response.data.data!=0){
              this.submitMessage = `注册成功,欢迎 ${this.form.username}! 账号为${response.data.data}`;
              this.dialogVisible = true;
            }
            else {
              this.submitMessage = `注册失败!`;
            this.dialogVisible = true;
            }
          }
          else {
            console.error(response.data.msg);
            this.submitMessage = `注册失败!`;
            this.dialogVisible = true;
          }
        })
        .catch(error => {
          console.error(error);
        });
    }
  }
};
</script>

<style scoped>
/* 容器居中 */
.register-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #f0f2f5;
}

/* 标题与副标题 */
.register-title {
  font-size: 26px;
  font-weight: bold;
  color: #333;
  margin-bottom: 10px;
}

.register-subtitle {
  font-size: 14px;
  color: #999;
  margin-bottom: 30px;
}

/* 表单容器 */
.form-box {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
}

/* 注册按钮 */
.el-button--primary {
  background-color: #409eff;
  border-color: #409eff;
  color: white;
  font-size: 16px;
}

/* 链接样式 */
a {
  color: #409eff;
}

/* 密码规则提示 */
.password-rules p {
  font-size: 12px;
  margin: 5px 0;
}

/* 对号标记 */
.valid {
  color: green;
}
</style>