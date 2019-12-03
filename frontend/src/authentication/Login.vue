<template>
  <div class="container">
    <center>
      
      <form class="lgn" id="lgn" v-bind:class="{small: resized}" @submit.prevent="login">
        <div class="sign">
          <h1 class="text" @click="$router.push('/')">Badgebook</h1><span class='si'>&nbsp;sign in</span>
        </div>
        <div class="loginbody">
          <div class="inputholder"  v-bind:class="{red: notfound}">
            <span>Username</span>
            <b-input type="text" required v-model="username"  v-bind:class="{unameErr: notfound}"/>
             <transition name="slide-fade">
            <span
              class="err"
              v-show="notfound"
            >This username is not found!</span>
          </transition>
          </div>

          <div class="inputholder"  v-bind:class="{red: wrongpass}">
            <span>Password</span>
            <b-input type="password" required v-model="password" v-bind:class="{unameErr: wrongpass}"/>
            <transition name="slide-fade">
            <span
              class="err"
              v-show="wrongpass"
            >This password is incorrect password!</span>
          </transition>
          </div>

          <b-button type="submit" class="lgnbtn" variant="primary">
            <span v-if="!loading">Login</span>
            <span v-else>
              <span>Loading&nbsp;</span>
              <b-spinner class="align-middle"></b-spinner>&nbsp;
            </span>
          </b-button>
        </div>
      </form>
      <div class="error"  v-bind:class="{small: resized}" v-show="netErr" >Network error! Please try again later.</div>
            <div class="sgn"  v-bind:class="{small: resized}">Don't have an account?&nbsp;<span @click="$router.push('/signUpAs')" class="sgnup">Sign up</span></div>

    </center>
  </div>
</template>


<script>
import $ from "jquery";
import axios from "axios";

export default {
  name: "login",
  data() {
    return {
      username: "",
      password: "",
      loading: false,
      resized: false,
      wrongpass: false,
      notfound: false,
      netErr: false,

    };
  },
  methods: {
    login() {
      this.loading = true;
      this.wrongpass = false;
      this.notfound = false;
      let cred = { username: this.username, password: this.password };
      this.$store
        .dispatch("login", cred)
        .then(res => {
          console.log(res);
          if (res == "Regular user") {
            console.log(res);
            this.loading = false;
            this.$router.push("/user");
          } else {
            this.$router.push("/organization");
          }
        })
        .catch(error => {
          this.loading = false;
          this.netErr = false;
          if (error.response != undefined) {
          if (error.response.status == 401) {
            this.wrongpass = true;
          } else if (error.response.status == 404) {
            this.notfound = true;
          } else {
             this.loading = false;
            alert(error.response.data.message);
          }
          }
          else {
            this.loading = false; 
            this.netErr = true;
          }
        });
    },
    handleResize() {
      if (window.innerWidth < 450) {
        this.resized = true;
      } else {
        this.resized = false;
      }
    }
  },
  created() {
    this.$emit("arrived");
    window.addEventListener("resize", this.handleResize);
    this.size = window.innerWidth;
    this.handleResize();
  },
  destroyed() {
    window.removeEventListener("resize", this.handleResize);
  },
  mounted() {
    $(".input").focus(function() {
      $(this)
        .parent()
        .find(".label-txt")
        .addClass("label-active")
        .css({ color: "#0071ff" });
    });

    $(".input").focusout(function() {
      if ($(this).val() == "") {
        $(this)
          .parent()
          .find(".label-txt")
          .removeClass("label-active");
      }
      $(this)
        .parent()
        .find(".label-txt")
        .css({ color: "#555657" });
    });
  }
};
</script>

<style scoped>
.sgnup {
  color:#005ac9;
  font-size: 18px;
  text-decoration: underline;
}

.si {
  font-size: 14px !important;
  display: inline;
}
.sgn {
  width: 360px;
  border-radius: 5px;
  padding-left: 20px;
  padding-right: 20px;
  /* border: 1px solid red; */
  color: #334350;
  font-size: 17px;
  /* border-bottom: 4px solid #f08d8d; */
  margin-top: 20px;
}

.slide-fade-enter-active {
  transition: all 0.5s ease;
}
.slide-fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}
.slide-fade-enter,
.slide-fade-leave-to {
  transform: translateX(10px);
  opacity: 0;
}

.unameErr {
  border: 1px solid red;
  background: #f7e4e4;
  color: red;
}
.red {
  color:red;
}
.err {
  font-weight: normal;
  color: #1a95ed;
  font-size: 11px;
  position: absolute;
  left: 0px;
  color: red;
}
.align-middle {
  color: white;
  height: 30px;
  width: 30px;
}

.submitted {
  color: rgb(3, 78, 133);
  background: #b7d4eb;
  border-radius: 5px;
  padding: 10px;
  font-size: 18px;
}

.ldword {
  font-weight: bold;
  font-family: verdana;
  color: rgb(3, 78, 133);
}
.inputholder {
  margin-top: 30px;
  margin-bottom: 30px;
  text-align: left;
  position:relative;
}
.lgnbtn {
  font-family: verdana;
  font-size: 18px;
}
.lgn {
  /* border: 1px solid lightgrey; */
  -webkit-box-shadow: 0px 1px 3px #74818f;
  box-shadow: 0px 1px 3px #74818f;
  width: 360px;
  /* margin-top: 150px; */
  /* margin-bottom: 40px; */
  background: white;
  padding: 0px;
  text-align: center;
  font-family: verdana;
  margin:0;
  border-radius: 0;
}

.loginbody {
  padding-left: 20px;
  padding-right: 20px;
  padding-bottom: 20px;
}
.error {
  width: 360px;
  border-radius: 5px;
  padding-left: 20px;
  padding-right: 20px;
  /* border: 1px solid red; */
  background: #f7e4e4;
  color: red;
  font-size: 17px;
  /* border-bottom: 4px solid #f08d8d; */
  margin-top: 20px;
}

.small {
  width: 100%;
}
.container {
  font-family: verdana;
  position: relative;
  justify-content: center;
  margin: 0;
  padding-top: 150px;
  /* width: 200vw; */
  /* background:lightblue; */
  height: 100vh;
}
.input {
  font-weight: normal;
}

label {
  display: block;
  position: relative;
  margin: 40px 0px;
}
.label-txt {
  position: absolute;
  top: -1.6em;
  font-weight: normal;
  font-family: verdana;
  padding: 10px;
  font-size: 16px;
  letter-spacing: 1px;
  color: #555657;
  transition: ease 0.2s;
  margin-top: 15px;
}





.input:focus + .line-box .line {
  width: 100%;
}

button:hover {
  background: #0071ff;
  color: #ffffff;
}

.sign {
  font-family: verdana;
  padding: 5px;
  margin: 0;
  color:white;
    background: #40a6e0;
}

.text {
  font-family: verdana;
  display: inline;
}
</style>






