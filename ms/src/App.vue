
<template>
  <div id="app">
    <router-view>
    </router-view>
    <LoginForm />
    <!-- <RegisterForm/> -->
  </div>
</template>

<script>
import LoginForm from './components/LoginForm.vue';
import RegisterForm from './components/RegisterForm.vue';
export default {
  components: {
    LoginForm,
    // RegisterForm
  },
};
</script>