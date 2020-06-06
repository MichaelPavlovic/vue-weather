<template>
  <div class="weather">
    <div class="search__box">
      <input type="text" class="search__bar" placeholder="Search..." v-model="search" v-on:keypress="fetchWeatherDataButton">
      <button type="submit" class="search__button" @click="fetchWeatherData"><i class="fa fa-search"></i></button>
    </div>
    <div class="weather__box" v-if="typeof weatherData.city != 'undefined'">
      <div class="flex-item">
        <div class="weather__location">{{ weatherData.city.name }}, {{ weatherData.city.country }}</div>
        <div class="weather__date">{{ getDate(weatherData.list[0].dt) }}</div>
        <div class="weather__time">{{ getTime(weatherData.city.timezone) }}</div>
        <div class="weather__windspeed">Wind Speed: {{ weatherData.list[0].wind.speed }} km/h</div>
        <div class="weather__winddirection">Wind Direction: {{ degreeToCardinal(weatherData.list[0].wind.deg) }}</div>
      </div>
      <div class="flex-item">
        <div class="weather__temp">{{ Math.round(weatherData.list[0].main.temp) }}°C</div>
        <div class="weather__tempF">{{ convertTemp(Math.round(weatherData.list[0].main.temp)) }}°F</div>
      </div>
      <div class="flex-item">
        <div class="weather__type">{{ weatherData.list[0].weather[0].main }}</div>
        <div class="weather__templow">Min: {{ Math.round(weatherData.list[0].main.temp_min) }}°C ({{ convertTemp(Math.round(weatherData.list[0].main.temp_min)) }}°F)</div>
        <div class="weather__temphigh">Max: {{ Math.round(weatherData.list[0].main.temp_max) }}°C ({{ convertTemp(Math.round(weatherData.list[0].main.temp_max)) }}°F)</div>
        <div class="weather__humidity">Humidity {{ weatherData.list[0].main.humidity }}%</div>
      </div>
    </div>
    <div class="weather__box-responsive" v-if="typeof weatherData.city != 'undefined'">
      <div class="weather__location">{{ weatherData.city.name }}, {{ weatherData.city.country }}</div>
      <div class="weather__type">{{ weatherData.list[0].weather[0].main }}</div>
      <div class="weather__tempbox">
        <div class="weather__temp">{{ Math.round(weatherData.list[0].main.temp) }}°C</div>
        <div class="weather__tempF">{{ convertTemp(Math.round(weatherData.list[0].main.temp)) }}°F</div> 
      </div>
      <div class="weather__date">{{ getDate(weatherData.list[0].dt) }}</div>
      <div class="weather__time">{{ getTime(weatherData.city.timezone) }}</div>
      <div class="weather__extra">
        <div class="weather__templow">Min: {{ Math.round(weatherData.list[0].main.temp_min) }}°C ({{ convertTemp(weatherData.list[0].main.temp_min) }}°F)</div>
        <div class="weather__temphigh">Max: {{ Math.round(weatherData.list[0].main.temp_max) }}°C ({{ convertTemp(weatherData.list[0].main.temp_max) }}°F)</div>
        <div class="weather__humidity">Humidity {{ weatherData.list[0].main.humidity }}%</div>
        <div class="weather__windspeed">Wind Speed: {{ weatherData.list[0].wind.speed }} km/h</div>
        <div class="weather__winddirection">Wind Direction: {{ degreeToCardinal(weatherData.list[0].wind.deg) }}</div>
      </div>
    </div>
    <div class="forecast__box" v-if="typeof weatherData.city != 'undefined'">
      <div class="flex-item">
        <div class="inline">
          <div class="forecast__date">{{ getDay(weatherData.list[7].dt) }}</div>
          <div class="forecast__type">{{ weatherData.list[7].weather[0].main }}</div>
        </div>
        <div class="inline">
          <div class="forecast__temp">{{ Math.round(weatherData.list[7].main.temp) }}°C ({{ convertTemp(Math.round(weatherData.list[7].main.temp)) }}°F)</div>
        </div>
      </div>
      <div class="flex-item">
        <div class="inline">
          <div class="forecast__date">{{ getDay(weatherData.list[15].dt) }}</div>
          <div class="forecast__type">{{ weatherData.list[15].weather[0].main }}</div>
        </div>
        <div class="inline">
          <div class="forecast__temp">{{ Math.round(weatherData.list[15].main.temp) }}°C ({{ convertTemp(Math.round(weatherData.list[15].main.temp)) }}°F)</div>
        </div>
      </div>
      <div class="flex-item">
        <div class="inline">
          <div class="forecast__date">{{ getDay(weatherData.list[23].dt) }}</div>
          <div class="forecast__type">{{ weatherData.list[23].weather[0].main }}</div>
        </div>
        <div class="inline">
          <div class="forecast__temp">{{ Math.round(weatherData.list[23].main.temp) }}°C ({{ convertTemp(Math.round(weatherData.list[23].main.temp)) }}°F)</div>
        </div>
      </div>
      <div class="flex-item">
        <div class="inline">
          <div class="forecast__date">{{ getDay(weatherData.list[31].dt) }}</div>
          <div class="forecast__type">{{ weatherData.list[31].weather[0].main }}</div>
        </div>
        <div class="inline">
          <div class="forecast__temp">{{ Math.round(weatherData.list[31].main.temp) }}°C ({{ convertTemp(Math.round(weatherData.list[31].main.temp)) }}°F)</div>
        </div>
      </div>
      <div class="flex-item">
        <div class="inline">
          <div class="forecast__date">{{ getDay(weatherData.list[39].dt) }}</div>
          <div class="forecast__type">{{ weatherData.list[39].weather[0].main }}</div>
        </div>
        <div class="inline">
          <div class="forecast__temp">{{ Math.round(weatherData.list[39].main.temp) }}°C ({{ convertTemp(Math.round(weatherData.list[39].main.temp)) }}°F)</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Weather',
  data() {
    return {
      api_key: process.env.VUE_APP_API_KEY,
      url_base: 'https://api.openweathermap.org/data/2.5/',
      search: '',
      weatherData: {}
    }
  },
  methods: {
    //fetch the weather data from the api and return the results as JSON
    fetchWeatherData: function(){
      fetch(`${this.url_base}forecast?q=${this.search}&units=metric&APPID=${this.api_key}`).then(results => {
        return results.json();
      }).then(this.setWeatherData);
    },
    //this method is for if the user presses enter instead of the search button
    fetchWeatherDataButton: function(e){
      if(e.key == "Enter"){
        fetch(`${this.url_base}forecast?q=${this.search}&units=metric&APPID=${this.api_key}`).then(results => {
          return results.json();
        }).then(this.setWeatherData);
      }
    },
    //assign the results to the weatherData object
    setWeatherData: function(results){
      //console.log(results);
      this.weatherData = results;
    },
    //get the date of the place searched for in a readable format
    getDate: function(dt){
      let date = new Date(dt * 1000); //date is returned in unixtime so multiply it by 1000
      const days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
      const months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];

      return `${days[date.getDay()]}, ${months[date.getMonth()]} ${date.getDate()}, ${date.getFullYear()}`;
    },
    getDay: function(dt){
      let date = new Date(dt * 1000); //date is returned in unixtime so multiply it by 1000
      const days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];

      return `${days[date.getDay()]}`;
    },
    //get the time of the place searched for in 12 hour time
    getTime: function(){
      let date = new Date();//date is returned in unixtime so multiply it by 1000
      let hours = date.getHours();
      let minutes = date.getMinutes();
      let meridiem = hours >= 12 ? 'PM' : 'AM';
      
      hours = hours % 12;
      hours = hours ? hours : 12;
      minutes = minutes < 10 ? '0'+minutes : minutes;

      return `${hours}:${minutes} ${meridiem}`;
    },
    //converts the degree of wind to a cardinal
    degreeToCardinal: function(deg) {
      let degree = (Math.floor((deg / 22.5) + 0.5)) % 16;
      var cardinals = ["N", "NNE", "NE", "ENE", "E", "ESE", "SE", "SSE", "S", "SSW", "SW", "WSW", "W", "WNW", "NW", "NNW"];
      return cardinals[degree];
    },
    //convert the temp from celsius to fahrenheit
    convertTemp: function(temp){
      return Math.round(temp * 1.8 + 32);
    }
  }
}
</script>

<style>
.weather{
  width:1000px;
  margin: 0 auto;
}
/* search bar */
.search__box {
  width: 100%;
  padding-top: 30px;
  color: white;
}
.search__box .search__bar {
  font-family: 'Roboto', sans-serif;
  width: 50%;
  font-size: 20px;
  padding: 10px;
  border-radius: 10px 0px 0px 10px;
  border: none;
  box-shadow: 0px 0px 8px;
  background-color: rgba(255, 255, 255, 0.4);
}
.search__box .search__button {
  font-family: 'Roboto', sans-serif;
  font-size: 20px;
  padding: 10px 25px;
  border:none;
  border-radius: 0px 10px 10px 0px;
  box-shadow: 0px 0px 8px;
  background-color: rgba(255, 255, 255, 0.4);
}
.search__box .search__bar:focus{
  background-color: rgba(255, 255, 255, 0.75);
}
.search__box .search__bar:hover{
  background-color: rgba(255, 255, 255, 0.75);
}
.search__box .search__button:hover{
  background-color: rgba(255, 255, 255, 0.75);
}
/* weather dashboard */
.weather__box {
  margin-top: 80px;
  color: white;
  text-shadow: 3px 4px rgba(0, 0, 0, 0.25);
  background-color:rgba(41, 41, 41, 0.596);
  padding: 30px;
  border-radius: 16px;
  display: flex;
  flex-direction: row;
}
.flex-item{
  flex-grow: 1;
}
.weather__box-responsive{
  display: none;
}
/* left column */
.weather__box .weather__location {
  padding-bottom: 8px;
}
.weather__box .weather__date {
  font-weight:300;
  padding-bottom: 8px;
}
.weather__box .weather__time {
  font-size: 16pt;
  padding-bottom: 30px;
  font-weight:700;
}
.weather__box .weather__windspeed {
  padding-bottom: 8px;
}
/* middle column */
.weather__box .weather__temp {
  font-size: 60pt;
  font-weight:700;
  padding: 0 25px;
}
.weather__box .weather__tempF {
  padding-top: 10px;
  font-size: 18pt;
}
/* right column */
.weather__box .weather__type {
  font-size: 24pt;
  font-weight:700;
  padding-bottom: 30px;
}
.weather__box .weather__temphigh {
  padding-bottom: 8px;
}
.weather__box .weather__templow {
  padding-bottom: 8px;
}
.weather__box .weather__humidity {
  padding-bottom: 8px;
}
/* forecast dashboard */
.forecast__box {
  margin-top: 80px;
  color: white;
  text-shadow: 3px 4px rgba(0, 0, 0, 0.25);
  background-color:rgba(41, 41, 41, 0.596);
  padding: 30px;
  border-radius: 16px;
  display: flex;
  flex-direction: column;
}
.forecast__box .flex-item{
  display: flex;
  text-align: center;
}
.forecast__box .inline{
  flex-grow: 1;
  display: inline-block;
  padding: 15px;
  border-top: 1px solid white;
}
.forecast__box .flex-item:nth-child(1) .inline{
  border-top: none;
}
.forecast__box .forecast__type {
  font-size: 16pt;
  font-weight:700;
  padding-top: 10px;
}
.forecast__box .forecast__temp {
  padding-top: 10px;
  font-size: 18pt;
}
/* Media queries */
@media only screen and (max-width: 1080px){
  .weather{
    width:700px;
  }
}
@media only screen and (max-width: 770px){
  .weather{
    width:500px;
  }
}
@media only screen and (max-width: 560px){
  .weather{
    width: 280px;
  }
  .weather__box{
    display: none;
  }
  .weather__box-responsive{
    display: block;
    color: white;
    text-shadow: 3px 4px rgba(0, 0, 0, 0.25);
  }
  .weather__tempbox{
    color: white;
    background-color:rgba(41, 41, 41, 0.596);
    padding: 10px;
    border-radius: 16px;
    margin-bottom: 30px;
  }
  .weather__box-responsive .weather__location {
    padding-bottom: 6px;
    padding-top: 40px;
    font-size: 18pt;
    font-weight: 300;
  }
  .weather__box-responsive .weather__type {
    font-size: 20pt;
    font-weight:400;
    padding-bottom: 15px;
  }
  .weather__box-responsive .weather__temp {
    font-size: 60pt;
    font-weight:700;
  }
  .weather__box-responsive .weather__tempF {
    padding-top: 5px;
    font-size: 18pt;
  }
  .weather__box-responsive .weather__date {
    font-weight:300;
    padding-bottom: 8px;
    font-size: 16pt;
  }
  .weather__box-responsive .weather__time {
    font-size: 16pt;
    padding-bottom: 30px;
    font-weight:400;
    font-size: 18pt;
  }
  .weather__extra {
    background-color:rgba(41, 41, 41, 0.596);
    padding: 25px;
    border-radius: 16px;
    margin-bottom: 30px;
    font-size: 16pt;
  }
  .weather__box-responsive .weather__templow {
    padding-bottom: 15px;
  }
  .weather__box-responsive .weather__temphigh {
    padding: 15px;
    border-top: 1px solid white;
  }
  .weather__box-responsive .weather__humidity {
    padding: 15px;
    border-top: 1px solid white;
  }
  .weather__box-responsive .weather__windspeed {
    padding: 15px;
    border-top: 1px solid white;
  }
  .weather__box-responsive .weather__winddirection {
    padding-top: 15px;
    border-top: 1px solid white;
  }
  @media only screen and (max-width: 320px){
  .weather{
    width:260px;
  }
}
}
</style>
