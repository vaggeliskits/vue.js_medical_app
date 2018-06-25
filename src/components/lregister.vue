<template>
  <v-app id="inspires">
    <v-toolbar color="teal" dark fixed app>
<!--top bar-->
      <v-toolbar-title>MedicalApp</v-toolbar-title>
    </v-toolbar>
    <v-content>
      <v-container
        justify-center
        align-center>
        <v-layout
          class="scrollbar_singin"
        >
          <v-app id="inspire" v-if="flag === 'log'">
            <v-form ref="form">
              <v-text-field v-model="login.email" prepend-icon="email" name="email" label="Email"
                            required></v-text-field>
              <v-text-field v-model="login.password" prepend-icon="lock" name="password" label="Password" id="password"
                            type="password" required></v-text-field>
              <v-progress-circular v-if="prog ===true" class="progress" indeterminate
                                   color="primary"></v-progress-circular>
              <v-card-actions>

                <v-layout row wrap>
                  <v-flex xs6>
                    <v-btn class="main_buttons" block color="teal" dark v-on:click.prevent="sign_in">Login</v-btn>
                  </v-flex>
                  <v-flex xs6>
                    <v-btn block color="teal" dark v-on:click.prevent="front">Register
                    </v-btn>
                  </v-flex>
                </v-layout>
              </v-card-actions>
              <br/>
              <!-- open dialog erros -->
              <v-dialog v-model="dialog" max-width="500px">
                <v-card>
                  <v-card-title>
                    <span>{{msg}}</span>
                    <v-spacer></v-spacer>
                  </v-card-title>
                  <v-card-actions>
                    <v-btn color="primary" flat @click.stop="dialog=false">Close</v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
            </v-form>
          </v-app>
          <!--Start registration form-->
          <v-app id="inspire" v-else-if="flag === 'reg'">
            <v-form ref="form">
              <v-text-field v-model="sign_up.user.email" prepend-icon="email" name="login" label="Email" type="text"
                            required></v-text-field>
              <v-text-field v-model="sign_up.user.password" prepend-icon="lock" name="password" label="Password"
                            id="reg_password" type="password" required></v-text-field>
              <v-text-field v-model="sign_up.user.password_confirmation" prepend-icon="lock" name="confirm password"
                            label="Confirm Password" id="conf_password" type="password" required></v-text-field>
              <v-text-field v-model="sign_up.user.full_name" prepend-icon="face" name="Full name" label="Full name"
                            id="fullname" type="text" required></v-text-field>

              <v-container fluid>
                <v-radio-group v-model="sign_up.user.gender" row>
                  <v-radio label="Male" value="male"></v-radio>
                  <v-radio label="Female" value="female"></v-radio>
                </v-radio-group>
              </v-container>

              <v-text-field v-model="sign_up.user.amka" prepend-icon="account_box" name="amka" label="amka" id="amka"
                            type="number" required></v-text-field>
              <v-progress-circular v-if="prog ===true" class="progress" indeterminate
                                   color="primary"></v-progress-circular>
              <v-card-actions>
                <v-layout row wrap>
                  <v-flex xs6>
                    <v-btn block color="teal" dark v-on:click.prevent="back">
                      <v-icon dark left>arrow_back</v-icon>
                      Back
                    </v-btn>
                  </v-flex>
                  <v-flex xs6>
                    <v-btn block color="teal" dark v-on:click.prevent="register">Register</v-btn>
                  </v-flex>
                </v-layout>
              </v-card-actions>
              <!-- open dialog erros -->
              <v-dialog v-model="dialog" max-width="500px">
                <v-card>
                  <v-card-title>
                    <span>{{msg}}</span>
                    <v-spacer></v-spacer>
                  </v-card-title>
                  <v-card-actions>
                    <v-btn color="primary" flat @click.stop="dialog=false">Close</v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
              <!-- end dialog errors -->
            </v-form>
          </v-app>
          <!--End registration form-->
        </v-layout>
      </v-container>
    </v-content>
    <v-footer color="teal" app>
      <span class="white--text">&copy; 2018</span>
    </v-footer>
  </v-app>

</template>

<script>
import VueResource from 'vue-resource'
import axios from 'axios';
import router from '../router'

export default {
  name: 'App',
  data(){
    postId : 1
    return{
      hovered: false,
      msg:'',
      e1: null,
       flag: 'log',
       prog: 'fals',
       dialog: false,
       alert: false,
      re: {
          email: '',
          full_name: ''
      },
      login: {
          email: '',
          password : ''
      },
      sign_up: {
        user: {
              email: '',
              password: '',
              password_confirmation: '',
              full_name: '',
              gender : '',
              amka : ''
            }
      },
      users:{
        user : {
        email: '',
        password: '',
        password_confirmation: '',

        }
      },
    }
  },
  methods: {
    // email validation
    validEmail:function(email) {
      var re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))+(\s{0,1})$/;
      return re.test(email);
    },
    //name validation
    validFullname:function(name){
      var re_name = /^[a-zA-Z-,]+(\s{0,1}[a-zA-Z-, ])*$/;
      return re_name.test(name);
    },
     //sign in method
     sign_in(){
      this.prog = true;
      this.alert = false;
      var checkstatuslogin
      const myjson  = new FormData();
      myjson.append('email',this.login.email);
      myjson.append('password',this.login.password);
      if(this.login.email == '' || this.login.password == '' || this.login.email == null || this.login.password == null){
        this.prog =false;
        this.dialog = true;
        this.msg='Email and password are required';
        console.log('this.login.email '+this.login.email);
        setTimeout(() => this.alert = true, 500);
        setTimeout(() => this.$refs.form.reset(), 500);
      }
      else if (this.login.password.length < 6){
        this.prog =false;
        this.dialog = true;
        this.msg='Password must be at least 6 characters';
        setTimeout(() => this.$refs.form.reset(), 500);
      }
      else {
        this.$http.post('api/users/sign_in',myjson, {emulateJSON:true} ).then(response =>{
          this.someData = response.json();
          checkstatuslogin = response.status;
          console.log(response.status);
          console.log(response.data);
          console.log('to eimail einai: '+response.data.email);
          this.re.email = response.data.email;
          this.re.full_name = response.data.full_name;
          router.push({name: "main" , params:{items:response.data}});
          this.prog = false;
        },(response) =>{

          if(response.status == 401) {
            this.$refs.form.reset();
            this.prog = false;
            this.dialog = true;
            this.msg = 'Invalid email or password.Please try again'
          }
          else{
            this.$refs.form.reset();
            this.prog = false;
            this.dialog = true;
            this.msg = 'Server error please try later.'
          }
        })
      }
      this.login.email === null;
      this.login.password ===null;
    },
    //register method
    register(){
      this.prog = true;
      var checkstatusregister
      const userinfo = new FormData();
      //create json object
      userinfo.append('email',this.sign_up.user.email);
      userinfo.append('password',this.sign_up.user.password);
      userinfo.append('password_confirmation',this.sign_up.user.password_confirmation);
      userinfo.append('full_name',this.sign_up.user.full_name);
      userinfo.append('gender',this.sign_up.user.gender);
      userinfo.append('amka',this.sign_up.user.amka);

       //validation for register form
       if(this.sign_up.user.email == '' || this.sign_up.user.password == '' || this.sign_up.user.password_confirmation == '' || this.sign_up.user.email == null || this.sign_up.user.password == null || this.sign_up.user.password_confirmation == null){
        this.prog = false;
        this.dialog = true;
        this.msg = 'Please complete all required fields and try again '
        setTimeout(() => this.$refs.form.reset(), 1);
       }
       else if(this.sign_up.user.full_name == '' || this.sign_up.user.gender == '' || this.sign_up.user.amka == '' || this.sign_up.user.gender == null || this.sign_up.user.full_name == null || this.sign_up.user.amka == null){
        this.prog = false;
        this.dialog = true;
        this.msg = 'Please complete all required fields and try again.'
        setTimeout(() => this.$refs.form.reset(), 1);
       }
         else if(this.sign_up.user.password != this.sign_up.user.password_confirmation){
         this.prog = false;
           this.dialog = true;
           this.msg = 'Incorrect password confirmationnnn'
         setTimeout(() => this.$refs.form.reset(), 1);
       }
        else if(!this.validEmail(this.sign_up.user.email)){
          this.prog = false;
          this.dialog =true;
          this.msg = "Email is invalid"
          setTimeout(() => this.$refs.form.reset(), 1);
        }
        else if(!this.validFullname(this.sign_up.user.full_name)){
          this.prog = false;
          this.dialog =true;
          this.msg = "Name must be character invalid"
          setTimeout(() => this.$refs.form.reset(), 1);
        }
        else if(this.sign_up.user.password.length < 6 || this.sign_up.user.password_confirmation.length < 6){
          this.prog = false;
          this.dialog = true;
          this.msg = 'Password must be at least 6 characters'
        setTimeout(() => this.$refs.form.reset(), 1);
        }
        else if(this.sign_up.user.amka.length < 11 || this.sign_up.user.amka.length > 11){
          this.prog = false;
          this.dialog = true;
          this.msg = 'Amka must be 11 numbers';
        setTimeout(() => this.$refs.form.reset(), 1);
        }
        else if(isNaN(this.sign_up.user.full_name) == false){
          this.prog = false;
          this.dialog = true;
          this.msg = 'Name must be character';
        setTimeout(() => this.$refs.form.reset(), 1);
        }
        else if(isNaN(this.sign_up.user.amka) == true) {
         this.prog = false;
         this.dialog = true;
         this.msg = 'Amka must be only number';
       }
        else {
          // post request
          this.$http.post('api/users',this.sign_up, {emulateJSON:true}).then(response =>{
            userinfo.someData = response.json();
            checkstatusregister = response.status;
            console.log(response.data.errors);
            router.push({name: "main" , params:{items:response.data}});
            this.prog = false;
          },(response) =>{
            //error server msg
            if(response.status == 504){
              this.prog = false;
              this.dialog = true;
              this.$refs.form.reset();
              this.msg = 'Server error please try later.';
            }
            else if(response.data.errors.amka == "has already been taken"){
              this.prog = false;
              this.dialog = true;
              this.$refs.form.reset();
              this.msg = 'Amka is not valid';
            }
            else if(response.data.errors.email == "has already been taken"){
              this.prog = false;
              this.dialog = true;
              this.msg = this.sign_up.user.email + ' Is already exist';
              this.$refs.form.reset();
            }
            else {
              this.prog = false;
              this.dialog = true;
              this.$refs.form.reset();
              this.msg = 'Server error please try later.';
            }
          })
        }
    },
    //back button
    back(){
      this.$refs.form.reset();
      this.flag = "log"
      this.alert = false;
      this.prog = false;
    },
    //front button
    front(){
      this.$refs.form.reset();
      this.alert = false;
      this.flag = 'reg'
      this.prog = false
    }
  },
  created(){

  },
  trigger() {
        setTimeout(() => { this.hovered = true }, 5000)
    }

}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#show-blogs{
  max-width: 800px;
  margin: 0 auto;
}
.single-blog{
  padding: 20px;
  margin: 20px 0;
  box-sizing: border-box;
  background: #8b9b5c;
}

.scrollbar_singin {
  max-height: 10px;
  display: block;
}

.buttons {
  width: 10px;
}
.main_buttons {
  /*border: 2px;*/
  border-style: solid;
  border-color: black;
}

    .progress {
    }
    /*.scroll-y{
      max-height: 700px;
      height: 700px;
      width: 400px;
      background: #555;
    }*/
</style>
