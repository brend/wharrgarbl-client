<template>
  <tool-bar v-model="language" @reset="resetGame()" />
  <wharrgarbl-game :lang="language" ref="game" />
</template>

<script>
import WharrgarblGame from './components/WharrgarblGame.vue'
import ToolBar from './components/tool-bar.vue'

export default {
  name: 'App',
  components: {
    WharrgarblGame,
    ToolBar,
  },
  data() {
    return {
      language: "en",
    };
  },
  methods: {
    resetGame() {
      this.$refs.game.reset();
    }
  },
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
