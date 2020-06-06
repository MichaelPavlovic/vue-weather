<template>
  <div id="app">
    <Weather />
  </div>
</template>

<script>
import Weather from './components/Weather';

export default {
  name: 'App',
  components: {
    Weather
  },
  mounted(){
    let date = new Date();
    let hours = date.getHours();

    if(hours >= 8 && hours <= 18){
      document.body.classList.remove('night');
      document.body.classList.add('day');
    } else {
      document.body.classList.remove('day');
      document.body.classList.add('night');
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;700&display=swap');
html, body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Roboto', sans-serif;
  height: 100%;
}
#app {
  text-align: center;
  padding: 30px;
}
.day {
  background-image: url('./assets/day.jpg');
  background-attachment: fixed;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
.night {
  background-image: url('./assets/night.jpg');
  background-attachment: fixed;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}
</style>
