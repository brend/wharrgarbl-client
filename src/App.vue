<template>
  <wharrgarbl-game />
</template>

<script>
import WharrgarblGame from './components/WharrgarblGame.vue'

export default {
  name: 'App',
  components: {
    WharrgarblGame
  }
}
</script>

<style>
body {
  background-color: #F7F7F7;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.no {
  color: darkgray;
}
.yes {
  color: green;
}
.elsewhere {
  color: rgb(206, 186, 28);
}
.error {
  color: red;
}
</style>
