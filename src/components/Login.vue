<template>
  <div>
    <form class="register-login-form center mx-auto" @submit.prevent="login">
      <h2 class="text-center mb-4">Log In</h2>
      <div class="form-group">
        <label>Email address</label>
        <input type="email" class="form-control" placeholder="Enter email" v-model="loginInput.email">
      </div>
      <div class="form-group">
        <label>Password</label>
        <input type="password" class="form-control" placeholder="Password" v-model="loginInput.password">
      </div>
      <button type="submit" class="btn btn-primary btn-block login-register"><i class="fas fa-sign-in-alt"></i></button>
      <p class="text-center mt-3">
        <a href="#" @click="changePageTo('register')">Create an Account</a>
      </p>
    </form>
  </div>
</template>

<script>
import axios from '../config/axios';

export default {
  name: 'Login',
  data(){
    return {
      loginInput: {email: '', password: ''}
    }
  },
  methods: {
    changePageTo(page){
      this.$emit('changePageTo', page)
    },
    login(){
      this.$emit('loadingPage', true);
      const data = {
        email: this.loginInput.email,
        password: this.loginInput.password
      }

      axios({
        method: 'POST',
        url: '/login',
        data
      })
        .then(res => {
          this.$emit('loadingPage', false);
          localStorage.access_token = res.data.access_token;
          this.loginInput.email = '';
          this.loginInput.password = '';
          this.changePageTo('home');
        })
        .catch(err => {
          this.$emit('loadingPage', false);
          this.changePageTo('register');
          this.$emit('renderErrorMessage', err.response.data);
        })
    }
  }

}
</script>

<style>

</style>

