<template>
  <div class="orgpage">
    <div id="Profile">
      <div class="prfl small">
        <div class="logo" v-bind:class="{smallLogo: resized2}">
          <img :src="url" alt class="profile" v-bind:class="{smallpro: resized2}" />
        </div>
      </div>
    </div>
    <div class="innercont" v-bind:class="{ftsize: resized}">
      <form @submit.prevent="register">
        <div class="accinfo">
          <div class="mrgnbtm">
            <span class="labl">Profile picture</span>
            <b-form-file class="binput" placeholder @change="onFileChange" ref="background"></b-form-file>
          </div>
          <div class="inputholder">
            <span>Email</span>
            <b-input type="email" class="binput" required v-model="orginfo.email"></b-input>
          </div>          
        </div>
        <div class="perinfo">
          <p class="sign2">About your organization*</p>

          <div class="inputholder">
            <span>Name</span>
            <b-input type="text" class="binput" required v-model="orginfo.orgName"></b-input>
          </div>
          <div class="inputholder">
            <span>Address</span>
            <b-input type="text" class="binput" required v-model="orginfo.address"></b-input>
          </div>
          <div class="inputholder">
            <span>Year started</span>
            <b-input type="number" class="binput" min="1800" required v-model="orginfo.years"></b-input>
          </div>
          <div class="inputholder">
            <span>What your organization do? | Description</span>
            <b-form-textarea type="text" rows="5" class="binput" required v-model="orginfo.description"></b-form-textarea>
          </div>
          <!-- <div class="noChsn" v-show="hasError">
            <p class="ertxt" v-if="noChosen">Are you working or studying? Please fill up.</p>
          </div> -->
          <b-button @click="$router.push('/organization')" class="lgnbtn" variant="danger">Cancel</b-button>&nbsp;
          <b-button type="submit" class="lgnbtn" variant="primary">
            <span v-if="!loading">Save changes</span>
            <span v-else>
              <span>Loading&nbsp;</span>
              <b-spinner class="align-middle"></b-spinner>&nbsp;
            </span>
          </b-button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import $ from "jquery";
import { required, minLength } from "vuelidate/lib/validators";

export default {
  name: "orgeditprofile",
  data() {
    return {
      orginfo: {
        username: "",
        password: "",
        confirmpassword: "",
        orgName: "",
        email: "",
        years: "",
        address: "",
        description: "",
        profilePic: ""
      },
      err: false,
      loading: false,
      type: "",
      resized: false,
      resized2: false,
      file: null,
      noChosen: false,
      hasError: false,
      url: null
    };
  },
  components: {
    // Footer
  },
  methods: {
    onFileChange(e) {
      this.file = e.target.files[0];
      this.url = URL.createObjectURL(this.file);
      console.log(this.file);
    },
    register() {
      var fd = new FormData();
      fd.append("img", this.file);
      fd.append("org", this.$store.getters.token);
      for (var key in this.orginfo) {
        fd.append(key, this.orginfo[key]);
      }

      if (this.url != null) {
        axios
          .post("http://localhost:8081/org/saveUpdate", fd, {
            headers: {
              "Content-Type": "multipart/form-data"
            }
          })
          .then(res => {
            alert("Succesfully Updated!")
            this.url = 'http://localhost:8081/static/' + res.data.profilePic
            this.orginfo = res.data
          })
          .catch(err => {
            alert(err);
          });
      } else {

        alert("Add Profile Picture!");
      }
    },
    validate() {
      this.loading = true;
      this.noChosen = this.school === "" && this.profession === "";
      return  !this.noChosen;
    },
    handleResize() {
      if (window.innerWidth < 1200) {
        this.resized2 = true;
        if (window.innerWidth <= 650) {
          this.resized = true;
        } else {
          this.resized = false;
        }
      } else {
        this.resized = false;
        this.resized2 = false;
      }
    }
  },
  destroyed() {
    window.removeEventListener("resize", this.handleResize);
  },
  created() {
    window.addEventListener("resize", this.handleResize);
    this.handleResize();
     axios
      .post("http://localhost:8081/org/updateOrg", {
        data: this.$store.getters.token
      })
      .then(res => {
        this.url = 'http://localhost:8081/static/' + res.data.profilePic
        this.orginfo = res.data;
        console.log(res)
      })
      .catch(err => {
        alert(err);
      });
    }
};
</script>


<style scoped>
.noChsn {
  padding: 10px;
  font-size: 13px;
  /* border: 1px solid red; */
  background: #f7e4e4;
  color: red;
  margin-bottom: 20px;
  border-radius: 5px;
}

.ertxt {
  padding: 0;
  margin: 0;
}
.ws {
  /* background:#fafcfc; */
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid lightgrey;
  border-radius: 5px;
}
.binput {
  background: #f5f9fa;
}
.lbl {
  font-size: 18px;
  margin-top: 35px;
  padding-bottom: 0;
  margin-bottom: 0;
}
.perinfo {
  /* background: white; */
  overflow: visible;
  padding: 0;
  padding-top: 0;
  margin: 0;
  width: 100%;
  height: 100%;
  position: relative;
  text-align: center;
}
.accinfo {
  width: 100%;
  padding: 0;
  position: relative;
  margin-top: 0;
}
.innercont {
  color: #385b73;
  padding: 0;
  border-radius: 0;
  width: 600px;
  background: white;
  text-align: center;
  padding: 40px;
}

.ftsize {
  width: 100%;
  padding: 10px;
}
.bdd {
  padding-top: 10px;
}

.genders {
  padding-left: 40px;
  padding-top: 0;
  padding-bottom: 0;
  position: relative;
  /* background:grey; */
  height: 10px;
}

.g {
  top: 10px;
  left: 20px;
  position: absolute;
}
.Gender {
  margin: 0;
  height: 10px;
  padding: 0;
}
.fncol {
  margin: 0;
  width: 50%;
}
.month {
  padding: 0px;
  display: none;
  position: absolute;
  border: 1px solid black;
  z-index: 9999;
  background: #f7fafa;
}
.inputholder {
  margin-top: 0;
  margin-bottom: 20px;
  text-align: left;
}
.unameErr {
  border: 1px solid red;
  background: #f7e4e4;
  color: red;
}

.m {
  margin: 0;
  padding: 3px;
  font-weight: normal;
  font-size: 15px;
  background: #f5f9fa;
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

.lgnbtn {
  font-family: verdana;
  font-size: 18px;
}

.lgt {
  color: red;
  text-decoration: underline;
}

hr {
  display: block;
  height: 1px;
  border: 0;
  border-top: 1px solid #b3b4b5;
  margin: 1em 0;
  padding: 0;
}

.uname {
  text-decoration: underline;
}
h5 {
  margin: 0;
  padding: 3px;
  font-weight: normal;
  font-size: 15px;
}

h5:hover {
  background-color: #0071ff;
  color: white;
}

.month {
  padding: 0px;
  display: none;
  position: absolute;
  border: 1px solid lightgray;
  z-index: 9999;
  background: #f7fafa;
}

#mnth {
  width: 100%;
  margin-left: 0;
}
#gend {
  font-size: 0.8em;
}

.bd {
  width: 100%;
}
.srow {
  margin-left: 0px;
  padding: 0;
  margin-top: 0;
  margin-bottom: 0;
  width: 100%;
}

.srow td {
  padding-top: 0;
  margin-top: 0;
  margin-bottom: 0;
  padding-bottom: 0;
  width: 50%;
}

#years {
  width: 150px;
  padding-left: 20px;
}

.gender {
  font-weight: normal;
}
.pholder {
  padding: 0;
}

.err {
  font-weight: normal;
  color: #1a95ed;
  font-size: 11px;
  position: absolute;
  left: 0px;
}
.red {
  color: red;
}

.fname {
  width: 100%;
  padding-right: 10px;
  margin: 0;
}
.ln {
  margin-top: 0;
  margin-bottom: 0;
  padding-top: 0;
  padding-bottom: 0;
}

#day {
  width: 10%;
}

.gg {
  margin-bottom: 0;
  /* height: 5px; */
  /* padding-top: 5px; */
  width: 100%;
  background: green;
}

.gg td {
  position: relative;
  padding: 0;
  margin: 0;
  /* width: 50%; */
}
.bday {
  width: 100%;
}

.bday td {
  padding: 0;
  width: 33%;
  padding: 1px;
}
.ln {
  margin-top: 0;
  margin-bottom: 0;
  padding-top: 0;
  padding-bottom: 0;
}

.lname {
  padding-left: 10px;
  width: 100%;
  margin: 0;
}
table {
  width: 100%;
  margin-top: 0;
  padding-top: 0;
  margin: 0;
  padding: 0;
  overflow: visible;
}

.body {
  padding: 0;
  margin: 0;
}

.sign {
  padding: 0px;
  margin-top: 0;
  font-size: 25px;
  font-weight: unset;
  font-family: verdana;
  font-weight: normal;
  margin-bottom: 0px;
}

.rutext {
  font-size: 30px;
}
.sign2 {
  padding: 0px;
  margin-top: 0;
  font-size: 25px;
  font-weight: unset;
  padding-top: 0;
  font-family: verdana;
  font-weight: normal;
  margin-bottom: 10px;
}

/* ================================== */

.username {
  font-size: 17px;
  margin-top: 0px;
  color: #315273;
  width: 100%;
}
div {
  font-family: verdana;
}

.orgn {
  color: #364452;
  margin-bottom: 0;
}

.address {
  font-size: 15px;
  color: #3b4d70;

  font-weight: bold;
}

.des2 {
  color: #1e4773;
}
.email {
  color: #486399;
  font-size: 15px;
}
.emailadd {
  text-decoration: underline;
}
.prof {
  float: left;
}
p {
  margin-bottom: 5px;
  margin-top: 5px;
}

.profile {
  /* height: 200px; */

  width: auto;
  height: 250px;
}

#Profile {
  height: 150px;
  /* background-image: url("~@/assets/orgbackground.jpg"); */
  background: #186fa1;
  /* background-size: cover; */
  font-family: verdana;
}

.logo {
  overflow: hidden;
  margin-top: 30px;
  width: 250px;
  margin-bottom: 10px;
  -webkit-box-shadow: 0px 1px 5px #0e222b;
  box-shadow: 0px 1px 5px #0e222b;
  height: 250px;
  border: 1px solid white;
  background: white;
}
.prfl {
  float: left;
  width: 25%;
  /* margin-left:25px; */
  /* margin-right: 25px; */
  margin-bottom: 0px;
  /* border-bottom: 2px solid #d5e7f5; */
  /* background:grey; */
}

.small {
  width: 100%;
  margin: 0;
}

.update {
  border-radius: 2px;
}
.smallLogo {
  width: 180px;
  height: 180px;
  overflow: hidden;
}
.smallpro {
  width: auto;
  height: 180px;
}

.orgpage {
  width: 100%;
  padding-left: 0;
  padding-right: 0;
  padding-top: 50px;
}
</style>





