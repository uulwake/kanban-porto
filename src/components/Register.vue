<template>
 <div>
     <form class="register-login-form center mx-auto" @submit.prevent="register">
      <h2 class="text-center mb-4">Register</h2>
      <div class="form-group">
        <label>Username</label>
        <input type="text" class="form-control" placeholder="Enter username" v-model="registerInput.username">
      </div>
      <div class="form-group">
        <label>Email address</label>
        <input type="email" class="form-control" placeholder="Enter email" v-model="registerInput.email">
      </div>
      <div class="form-group">
        <label>Password</label>
        <input type="password" class="form-control" placeholder="Password" v-model="registerInput.password">
      </div>
      <button type="submit" class="btn btn-primary btn-block login-register"><i class="fas fa-sign-in-alt"></i></button>
      <h5 class="text-center my-3">Or</h5>

      <!-- google sign in -->
      <g-signin-button
        :params="googleSignInParams"
        @success="onSignInSuccess"
        @error="onSignInError">
        Sign in with Google
      </g-signin-button>

      <p class="text-center mt-3">
        <a href="#" @click="changePageTo('login')">I Already Have an Account</a>
      </p>
    </form>

  </div>
  
</template>

<script>
import axios from '../config/axios';
import GSignInButton from 'vue-google-signin-button'

// function 

export default {
  name: 'Register',
  data(){
    return {
      registerInput: {username: '', email: '', password: ''},
      googleSignInParams: {
        client_id: '879200961151-fd2se3re4vug4ajeoncpcattah4abal7.apps.googleusercontent.com'
      }
    }
  },
  methods: {
    changePageTo(page){
      this.$emit('changePageTo', page)
    },
    register(){
      this.$emit('loadingPage', true);
      const data = {
        username: this.registerInput.username,
        email: this.registerInput.email,
        password: this.registerInput.password
      }
      axios({
        method: 'POST',
        url: '/register',
        data
      })
        .then(res => {
          this.$emit('loadingPage', false);
          this.changePageTo('home');
          localStorage.access_token = res.data.access_token;
          this.registerInput.username = '';
          this.registerInput.email = '';
          this.registerInput.password = '';
        })
        .catch(err => {
          this.$emit('loadingPage', false);
          this.changePageTo('register');
          this.$emit('renderErrorMessage', err.response.data);
        })
    },
    onSignInSuccess (googleUser) {
      const id_token = googleUser.getAuthResponse().id_token;
      axios({
        method: 'POST',
        url: '/gSignIn',
        data: {
          id_token
        }
      })
        .then(res => {
          localStorage.access_token = res.data.access_token;
          this.changePageTo('home');
        })
        .catch(err => {
          console.log(err);
          this.$emit('renderErrorMessage', err.response.data);
        })
    },
    onSignInError (error) {
      console.log('OH NOES', error);
      this.$emit('renderErrorMessage', err);

    }
    
  }

}
</script>

<style scoped>
.g-signin-button {
  display: inline-block;
  padding: 4px 8px;
  border-radius: 3px;
  background-color: #3c82f7;
  color: #fff;
  box-shadow: 0 3px 0 #0f69ff;
  width: 100%;
  text-align: center;
  height: 2rem;
}

.g-signin-button:hover{
  cursor: pointer;
}

</style>

