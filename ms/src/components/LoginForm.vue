<template>
  <div id="app" class="register-container">
    <!-- 标题与副标题 -->
    <h2 class="register-title">欢迎登陆秒杀系统</h2>
    <p class="register-subtitle">每天，乐在抢购。</p>

    <!-- 登录表单 -->
    <el-form :model="form" ref="form" class="form-box">
      <!-- 用户名输入框 -->
      <el-form-item :error="useridError">
        <el-input v-model="form.userid" placeholder="账号" style="width: 300px" @blur="checkUserid"></el-input>
      </el-form-item>

      <!-- 密码输入框 -->
      <el-form-item :error="passwordError">
        <el-input v-model="form.password" placeholder="密码" type="password" style="width: 300px"
          @blur="checkPassword"></el-input>
      </el-form-item>
      <!-- 协议复选框 -->
      <el-form-item>
        <el-checkbox v-model="form.agree">
          我已阅读并同意
          <a href="#">服务协议</a> 和
          <a href="#">隐私政策</a>
        </el-checkbox>
      </el-form-item>

      <!-- 登录按钮 -->
      <el-form-item>
        <el-button type="primary" @click="submitForm" style="width: 300px">
          立即登录
        </el-button>
      </el-form-item>
      <!-- 注册按钮 -->
      <el-form-item>
        <!-- <router-view /> -->
        <el-button type="primary" @click="gotoregister" style="width: 300px">
          注册账户
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
        userid: '',
        password: '',
        agree: false
      },
      useridError: '',
      passwordError: '',
      dialogVisible: false,
      submitMessage: '',
    };
  },
  methods: {
    // 检查用户名
    checkUserid() {
      if (!this.form.userid) {
        this.useridError = '账号不能为空';
      } else {
        this.useridError = '';
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
      if (!this.form.userid || !this.form.password || !this.form.agree) {
        this.submitMessage = '登录失败，请填写完整信息并同意服务协议。';
        this.dialogVisible = true;
      } 
      
      const user = { id: parseInt(this.form.userid, 10), password: this.form.password, username: "1" };
      axios.post('http://localhost:28080/users_query', user)
        .then(response => {
          if (response.data.code === 200) {
            this.login=response.data.data;
            this.dialogVisible = true;
            //判断登录是否成功
            if(this.login){
                this.submitMessage = `登录成功，欢迎 ${this.form.userid}!`;
                this.dialogVisible = true;
            }
            else {
              this.submitMessage = '登录失败,用户名或密码错误!';
              this.dialogVisible = true;
            }
          } else {
            console.error(response.data.msg);
            this.submitMessage = '登录失败，请填写完整信息并同意服务协议。';
            this.dialogVisible = true;
          }
        })
        .catch(error => {
          console.error(error);
        });
      
    },
    gotoregister() {
      // this.dialogVisible = true;
      this.$router.push({ name: 'register' });
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
</style>