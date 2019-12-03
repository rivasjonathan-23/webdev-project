<template>
  <div id="app">
    <div class="jhead" v-if="!inlogin">
      <div class="jwebname" @click="redirect('/')">Badgebook</div>
      <div class="pholder" v-bind:class="{resh: resized,show: clicked}">
        <p
          class="jb"
          v-bind:class="{resp: resized}"
          v-show="!this.$store.getters.isLoggedIn"
          @click="redirect('/login')"
        >Sign in</p>
        <p class="jb"  v-show="this.$store.getters.isLoggedIn" v-bind:class="{resp: resized}" @click="signout">Sign out</p>

        <p
          class="jb"
          v-bind:class="{resp: resized}"
          v-show="!this.$store.getters.isLoggedIn"
          v-if="!insignup"
          @click="redirect('/signUpAs')"
        >Sign up</p>
        <p class="jb" v-bind:class="{resp: resized}" @click="$bvModal.show('searchUser')">Search</p>
      </div>
      <div class="showb" v-if="resized" @click="togle">=</div>
    </div>

    <b-modal
      id="searchUser"
      title="Search other user | organization"
      no-close-on-esc
      no-close-on-backdrop
      hide-header-close
      centered
      hide-footer
    >
      <b-form>
        <b-form-input id="searchb" v-model="person" placeholder="Enter username"></b-form-input>
      </b-form>
      <b-button class="closeSearch" block @click="$bvModal.hide('searchUser')">Exit</b-button>
    </b-modal>
    <div>
      <center>
        <router-view :userInfo="info" @arrived="locate"/>
      </center>
    </div>
  </div>
</template>


<script>
// eslint-disable-next-line
/* eslint-disable */
import axios from "axios";
import UserProfile from "./modules/user/Profile.vue";
import OrgProfile from "./modules/organization/orgprofile.vue";

export default {
  name: "app",
  props: {
    info: Object
  },
  data() {
    return {
      person: "",
      notlogin: true,
      isloginConfirm: false,
      inlogin: false,
      insignup: false,
      inlogin: false,
      path: "",
      resized: false,
      clicked: true
    };
  },
  components: {
    UserProfile,
    OrgProfile
  },
  methods: {
    getData(data) {
      this.info = data;
    },

    togle() {
      if (this.clicked) {
        this.clicked = false;
      } else {
        this.clicked = true;
      }
    },
    redirect(path) {
      this.$router.push(path);
      this.path = path;
      this.locate();
    },
    locate() {
      if (this.$router.currentRoute.path == "/login") {
        this.inlogin = true;
        this.insignup = false;
      } else if (this.$router.currentRoute.path == "/signUpAs") {
        this.inlogin = false;
        this.insignup = true;
      } else {
        this.inlogin = false;
        this.insignup = false;
      }
    },
    search() {
      let user = { user: this.person };
      axios.post("http://localhost:8081/search", user).then(
        response => {
          if (response.data.respond != "Cannot find user!") {
            console.log(response.data);
            alert(response.data.respond);
          } else {
            alert(response.data.respond);
          }
        },
        err => {
          console.log("error");
        }
      );
    },
    signout() {
      this.$store.dispatch("logout").then(() => {
        this.$router.push("/login");
        this.notlogin = true;
        this.isloginConfirm = false;
        this.isRuser = false;
        this.isOrg = false;
      });
    },
    handlesize() {
      if (window.innerWidth <= 500) {
        this.resized = true;
      } else {
        this.resized = false;
      }
    }
  },
 
  watch: {
    path() {
      if (this.$router.currentRoute.path == "/login") {
        this.inlogin = true;
      } else {
        this.inlogin = false;
      }
    }
  },
  destroyed() {
    window.removeEventListener("locate", this.locate);
    window.removeEventListener("resize", this.handlesize);
  },
  created() {
    window.addEventListener("resize", this.handlesize);
    this.handlesize();
    window.addEventListener("locate", this.locate);
    this.locate();
    var url = window.location.href;
    if (url.includes("/user")) {
      this.notlogin = false;
      this.isloginConfirm = true;
      this.isRUser = true;
      this.isOrg = false;
      // this.$router.push("/user/mybadge")
    } else if (url.includes("organzation")) {
      this.notlogin = false;
      this.isloginConfirm = true;
      this.isRUser = false;
      this.isOrg = true;
    }
  }
};
</script>

<style scoped>
.jhead {
  background: #186fa1;
  color: white;
  letter-spacing: 1px;
  z-index: 1;
  width: 100%;
  /* position: relative; */
  font-family: verdana;
  height: 51px;
  text-align: right;
  position: absolute;
}

.jitem {
  list-style: none;
}

.pholder {
  margin: 0;
  transition: 0.3s ease;
}

.jb {
  padding: 15px !important;
  float: right;
}

.resp:hover {
  background: dodgerblue;
}

.showb {
  padding: 0px 15px;
  position: absolute;
  z-index: 1;
  right: 1%;
  font-size: 35px;
  cursor: pointer;
  transition: ease 0.1s;
}

.showb:hover {
  text-shadow: 5px 5px 5px black;
}

.showb:active {
  text-shadow: unset;
}

.resh {
  padding-top: 50px;
  background: #2090d1;
  position: absolute;
  display: none;
  right: 2%;
  top: 0;
  z-index: 1;
}

.show {
  display: unset;
}

.resp {
  padding: 10px;
  float: unset;
  display: block;
  z-index: 1;
  margin: 0;
}
.jwebname {
  position: absolute;
  padding: 9px;
  font-size: 20px;
  transition: 0.3s ease;
  left:2%;
}
.jwebname:hover {
  text-shadow: 5px 5px 5px black;
}
.closeSearch {
  width: 90px;
  float: right;
}

.closeSearch:hover {
  background-color: rgb(3, 78, 133, 0.9);
  border-color: 1px solid rgb(3, 78, 133, 0.9);
}

#searchb {
  padding-left: 10px;
  border: 1px solid gray;
}

.opt {
  margin: 0;
  color: white;
  padding: 7px;
  padding-left: 5px;
  padding-right: 5px;
}

.opt:hover {
  background: gray;
}
.icon {
  padding-top: 0;
  display: none;
}



.searchbox {
  /* margin: 10px; */
  padding: 0;
}

.content {
  height: 100vh;
  /* width: 100vw; */
  text-align: center;
  /* background-image: url("~@/assets/bb/3.jpg"); */
  /* background-size: cover; */
  /* background-attachment: fixed; */
  /* background:lightblue; */
}

#app,
body,
html {
  height: 100vh;
  font-family: verdana;
}

#srch {
  margin: 0;
  font-size: 13px;
  padding: 8px;
  width: 190px;
}

.fa:hover {
  background-color: lightblue;
  border-radius: 25px;
}
p {
  cursor: pointer;
  transition: ease 0.3s;
}
.btn:hover {
  border-color: gray;
}

p:hover {
  text-shadow: 5px 5px 5px black;
}

#search {
  margin: 12px;
}

#search:hover {
  box-shadow: 5px 5px 5px #1b2c3d;
}

.navbar {
  z-index: 1000;
  position: absolute;
  width: 100%;
  margin-top: 0px;
  border-radius: 0;
  padding-right: 2%;
  padding-left: 2%;
  margin-bottom: 0;
  background: #186fa1;
  padding-top: 0;
  padding-bottom: 0;
  overflow: hidden;
}

.btn {
  float: right;
  padding: 5px;
  background-color: transparent;
  border: 1px solid #e0e0e0;
  margin: 8px;
  cursor: pointer;
  transition: ease 0.3s;
}

#su {
  padding-right: 0px;
  border: none;
}

ul {
  padding: 0;
  margin: 0;
}

body {
  zoom: 10%;
}

.auth {
  margin: 5px;
  margin-top: 2px;
  margin-bottom: 2px;
  font-size: 17px;
  color: white;
}

.singout {
  margin: 5px;
  margin-top: 2px;
  margin-bottom: 2px;
  font-size: 17px;
  color: white;
  font-weight: normal;
  padding-right: 10px;
}

.web-name {
  padding: 8px;
  color: white;
  letter-spacing: 3px;
  margin: 2px;
}

button {
  margin-left: 0px;
}

button {
  display: inline-block;
  padding: 12px 24px;
  background: rgb(220, 220, 220);
  font-weight: bold;
  color: rgb(120, 120, 120);
  border: none;
  outline: none;
  border-radius: 3px;
  cursor: pointer;
  transition: ease 0.3s;
}

/* #nav {
  width: 450px;
  text-align: right;
} */

button:hover {
  background: #5d5f61;
  color: #ffffff;
}
</style>
