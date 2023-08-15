<template>
  <div class="center">
    <h1>Sign Up</h1>
    <form @submit.prevent method="post">
      <div class="txt_field">
        <input v-model="username" type="text" required />
        <span></span>
        <label>Username</label>
      </div>
      <div class="txt_field">
        <input v-model="email" type="email" required />
        <span></span>
        <label>Email Address</label>
      </div>
      <div class="txt_field">
        <input v-model="city" type="text" required />
        <span></span>
        <label>City</label>
      </div>
      <div class="txt_field">
        <input v-model="password" type="password" required />
        <span></span>
        <label>Password</label>
      </div>
      <div class="txt_field">
        <input v-model="confirmPassword" type="password" required />
        <span></span>
        <label>Confirm Password</label>
      </div>
      <input @click="register()" type="submit" value="Register" />

      <div class="signup_link">
        Do you have account? <router-link to="Login">Sign in</router-link>
      </div>
    </form>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      username: "",
      email: "",
      city: "",
      password: "",
      confirmPassword: "",
    };
  },
  methods: {
    async register() {
      try {
        const { username, email, city, password, confirmPassword } = this;
        axios.post("http://127.0.0.1:5000/api/register", {
          username,
          email,
          city,
          password,
          confirmPassword,
        });
        this.$router.push({ name: "Login" });
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Noto+Sans:wght@700&family=Poppins:wght@400;500;600&display=swap");
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Poppins", sans-serif;
}
body {
  margin: 0;
  padding: 0;
  background-color: #adadad;
  height: 100vh;
  overflow: hidden;
}
.center {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 400px;
  background: white;
  border-radius: 10px;
  box-shadow: 10px 10px 15px 10px rgba(0, 0, 0, 0.05);
}
.center h1 {
  text-align: center;
  padding: 20px 0;
  border-bottom: 1px solid silver;
}
.center form {
  padding: 0 40px;
  box-sizing: border-box;
}
form .txt_field {
  position: relative;
  border-bottom: 2px solid #adadad;
  margin: 30px 0;
}
.txt_field input {
  width: 100%;
  padding: 0 5px;
  height: 40px;
  font-size: 16px;
  border: none;
  background: none;
  outline: none;
}
.txt_field label {
  position: absolute;
  top: 50%;
  left: 5px;
  color: #adadad;
  transform: translateY(-50%);
  font-size: 16px;
  pointer-events: none;
  transition: 0.5s;
}
.txt_field span::before {
  content: "";
  position: absolute;
  top: 40px;
  left: 0;
  width: 0%;
  height: 2px;
  background: #2691d9;
  transition: 0.5s;
}
.txt_field input:focus ~ label,
.txt_field input:valid ~ label {
  top: -5px;
  color: #2691d9;
}
.txt_field input:focus ~ span::before,
.txt_field input:valid ~ span::before {
  width: 100%;
}
.pass {
  margin: -5px 0 20px 5px;
  color: #a6a6a6;
  cursor: pointer;
}
.pass:hover {
  text-decoration: underline;
}
input[type="submit"] {
  width: 100%;
  height: 50px;
  border: 1px solid;
  background: #2691d9;
  border-radius: 25px;
  font-size: 18px;
  color: #e9f4fb;
  font-weight: 700;
  cursor: pointer;
  outline: none;
}
input[type="submit"]:hover {
  border-color: #2691d9;
  transition: 0.5s;
}
.signup_link {
  margin: 30px 0;
  text-align: center;
  font-size: 16px;
  color: #666666;
}
.signup_link a {
  color: #2691d9;
  text-decoration: none;
}
.signup_link a:hover {
  text-decoration: underline;
}
</style>
