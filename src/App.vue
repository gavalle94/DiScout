<template>
  <main>
    <vue-header/>
    <vue-navbar :loggedIn="loggedIn" :userInfo="userInfo" v-on:changepage="changePage" v-on:logout="logout"/>
    <component v-bind:is="whichPage" v-on:userLoggedIn="logIn" v-on:changepage="changePage"/>
    <vue-footer/>
  </main>
</template>

<script>
  import Header from './components/Header'
  import NavBar from './components/NavBar'
  import Footer from './components/Footer'
  import SearchPage from './components/SearchPage'
  import Login from './components/Login'
  import GraphPage from './components/GraphPage'
  import Registration from './components/Registration'
  import UserSettings from './components/UserSettings'
  import News from './components/News'
  import { bus } from './main'

  export default {
    name: 'App',
    components: {
      'vue-header': Header,
      'vue-navbar': NavBar,
      'vue-footer': Footer,
      'vue-search-page': SearchPage,
      'vue-graph-page': GraphPage,
      'vue-login-page': Login,
      'vue-registration-page': Registration,
      'vue-user-settings': UserSettings,
      'vue-news': News
    },
    data() {
      return{
        whichPage: 'vue-search-page',
        loggedIn: false,
        userInfo: {},
        tracked_products: []
      }
    },
    created(){
      let user=window.sessionStorage.getItem('user');
      if(user!==null){
        this.loggedIn=true;
        this.userInfo=JSON.parse(user)
      }

      // create the DB
      Plotly.d3.json('static/database_production/product_db.json', function (e, data) {
        window.sessionStorage.setItem('database', JSON.stringify(data.products));
        bus.$emit("DBLOADED")
      });


    },
    methods: {
      logout: function(){
        this.loggedIn=false;
        window.sessionStorage.removeItem('user');
        this.changePage('Home')
      },
      logIn: function (data, arrivalPage='vue-search-page') {
        this.loggedIn=true;
        this.userInfo=data;
        window.sessionStorage.setItem('user', JSON.stringify(data));
        this.whichPage=arrivalPage
      },
      changePage: function(targetPage){
        if(targetPage === "Home"){
          this.whichPage='vue-search-page'
        }
        else if(targetPage === "MyTrack"){
          if(this.loggedIn){
            this.whichPage ='vue-graph-page'
          }
          else{
            sessionStorage.setItem("arrivalPage", "vue-graph-page");
            this.whichPage ='vue-login-page'
          }
        }
        else if(targetPage === "Login"){
          this.whichPage='vue-login-page'
        }
        else if(targetPage === "Registration"){
          this.whichPage='vue-registration-page'
        }
        else if(targetPage === "News"){
          this.whichPage = 'vue-news'
        }
        else if(targetPage === "UserSettings"){
          this.whichPage = 'vue-user-settings'
        }
      }
    }
  }


</script>

<style>

</style>
