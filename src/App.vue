<template>
  <div id="app">
    <div class="loader" v-if="loader">
      <img src="./assets/loader.gif" alt="">
    </div>
    <Header @sendCity="getCity"/>
    <SelectedCity/>
  </div>
</template>
//ggg
<script>
var self;
import Header from './components/header'
import SelectedCity from './components/selected-city'
const axios = require('axios');
export default {
    name: 'app',
    data(){
      return {
        city:"",
        loader:true
      }
    },
    components: {
      Header,
      SelectedCity
    },
    methods:{
      getCity(city){
        this.city = city;
        getWeatherData(this.city, this)
      }
    },
    mounted(){
      getWeatherData((Intl.DateTimeFormat().resolvedOptions().timeZone).split("/")[1] ,this)
      self = this;
    }
  }
  const getWeatherData = (city, self)=>{
    axios
        .get(`${'https://cors-anywhere.herokuapp.com/'}https://api.openweathermap.org/data/2.5/forecast?q=${city}&appid=75a1d9176ca419ff6941b216d269a1d9`)
     // .get(`https://api.openweathermap.org/data/2.5/forecast/daily?id=524901=Yerevan&mode=xml&units=metric&cnt=7&appid=4f4e5192f08f0d169e8a37b63df5947d`)
        .then(response => {
          self.loader = false;
          self.$emit('updateResponse', response.data); 
          
        })
        .catch(error => {
          alert("No Item")
      });
    }
</script>

<style lang="scss">
  body{
    background-color: beige;
    
  }
  .loader{
    position: fixed;
    left: 0;
    top: 0;
    opacity: 1;
    background-color: #fff;
    width: 100%;
    height: 100%;
    z-index: 222;
    display: flex;
    justify-content:center;
    align-items: center
  }
  *{
    box-sizing: border-box;
    padding: 0;
    margin: 0;
    font-family: 'Text Me One', sans-serif;
  }
</style>
