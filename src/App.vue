<template>
  <div id="app" >
    <div class = "header">
      <input class="input" v-model="city" v-on:keyup.enter="search" placeholder="Enter the city" />
      <img src="./assets/search.svg" class="search_icon" @click="search"/>
    </div>
    <div class = "result"  v-if="data" :class="typeof this.data != 'null' && parseInt(this.data.temp.real) > 16 ? 'warm' : 'cold'">  
      <div class="main_info">
        <div>{{ data.city}}, {{ data.country}}</div>
        <div>{{data.time.day}} {{data.time.time}}</div>
        <div>{{data.weather}}</div>
      </div>
      <div class="row">
        <div class="temp">
          <div>Temperature: {{data.temp.real}}</div>
          <div>Feels like: {{data.temp.feels_like}}</div>
        </div>
        <div class="second">
          <div>Humidity: {{data.temp.humidity}}</div>
          <div class="wind">
            <div>Wind: {{data.wind.speed}}</div>
            <svg width="1em" height="1em" viewBox="0 0 16 16" :style="style" class="bi bi-arrow-right" fill="currentColor" xmlns="http://www.w3.org/2000/svg">
              <path fill-rule="evenodd" d="M1 8a.5.5 0 0 1 .5-.5h11.793l-3.147-3.146a.5.5 0 0 1 .708-.708l4 4a.5.5 0 0 1 0 .708l-4 4a.5.5 0 0 1-.708-.708L13.293 8.5H1.5A.5.5 0 0 1 1 8z"/>
            </svg>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  data(){
    return{
      data: null,
      city: '',
      deg: null
    }
  },
  methods: {
    search(){
      var d = new Date();
      var days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
      
      fetch(`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=dc1e35bf5c26b01c5bbe91dd1c848668`)
        .then(res => res.json())
        .then(data => {
          this.data = {
            city: data.name,
            country: data.sys.country,
            time: {
              day: days[d.getDay()],
              time: `${(data.timezone / 3600) + d.getHours() - 4}:${d.getMinutes() < 10 ? '0' + d.getMinutes() : d.getMinutes() }` 
            },
            weather: data.weather[0].main,
            wind: {
              speed: (data.wind.speed*3.6).toFixed(2) + ' km/h',
              degree: data.wind.deg 
            },
            temp: {
              real: Math.ceil(data.main.temp - 273.15) + ' °C',
              feels_like: Math.ceil(data.main.feels_like -273.15) + ' °C',
              humidity: data.main.humidity + ' %'
            }

          }
        })
        .catch(err => console.log(err));

      this.city = '';
      document.querySelector('.input').focus();
    }
  },
  computed: {
     style () {
        return { transform: 'rotate(-' + this.data.wind.degree + 'deg)'}
     }
  }
}
</script>

<style>
  body{
    margin: 0;
  }

  #app {
    /*font-family: Avenir, Helvetica, Arial, sans-serif;*/
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    width: 100%;
    height: 100vh;
    overflow: hidden;
    transition: all 1s ease-out;
    font-family: 'Lobster', cursive;
    font-size: 25px !important;
  }

  .header{
    width: 100%;
    height: 50px;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .input{
    border: .2px solid black;
    border-radius: 10px;
    box-sizing: border-box;
    height: 35px;
    width: 30%;
    padding: 5px 10px;
    font-size: 19px;
    font-weight: 500;
  }

  .search_icon{
    margin-left: -30px;
    width: 25px;
    height: 25px;
  }

  .search_icon:hover{
    cursor: pointer;
  }

  .result{
    height: 92%;
    display: flex;
    flex-direction: column;
    width: 100%;
    align-items: center;
  }


  .main_info{
    height: 80px;
    width: 180px;
    font-size: 20px;
    font-weight: 500;
    padding: 5px 10px;
    margin-top: 50px;
  }

  .main_info > div:not(last-child),
  .temp > div:not(last-child),
  .second > div:not(last-child){
    margin-bottom: 5px;
  }

  .row{
    width: 100%;
    display: flex;
    flex-direction: row;
    justify-content: center;
    margin-top: 80px;
  }

  .temp,
  .second{
    font-size: 18px;
    font-weight: 500;
    padding: 5px 10px;
  }

  .second{
    margin-left: 120px;
  }

  .wind{
    display: flex;
    flex-direction: row;
    align-items: center;
  }

  .wind > div:not(last-child){
    margin-right: 15px;
  }

  .cold{
    background: url('./assets/cold.jpg');
  }

  .warm{
    background: url('./assets/warm.jpg');
    color: white !important;
  }

  @media only screen and (max-width: 600px){
    .input{
      width: 40%;
    }
  }


  @media only screen and (max-width: 460px){
    .input{
      width: 50%;
    }

    .row{
      flex-direction: column;
    }

    .second{
      margin-left: 0;
      align-items: center;
      align-self: center;
      margin-top: 40px;
    }
  }
</style>
