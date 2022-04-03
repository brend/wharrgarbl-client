<template>
  <div class="hello">
    <h1>It is Wharrgarbl!</h1>
    <h3>Please enter your guess below</h3>
    <div v-if="!done">
      <input ref="guess" v-model="guess">
      <button @click="sendGuess()">Guess!</button>
      <p v-if="errorMessage" class="error">{{errorMessage}}</p>
    </div>
    <div v-if="done">
      <button @click="reset()">Play another game</button>
    </div>

    <ul>
      <li v-for="(item, index) in items" :key="index">
        <span v-for="(c, ci) in item.text.split('')" 
          :key="ci"
          :class="color(item, ci)"
        >
          {{c}}
        </span>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'WharrgarblGame',
  data() {
    return {
      guess: '',
      done: false,
      errorMessage: null,
      items: [],
      state: {
        hints: {},
      },
      defaultState: {
        hints: {},
      },
    };
  },
  methods: {
    async debug() {
      for (const word of ['ALBUM', 'THEIR', 'KNOWS', 'PODGY']) {
        this.guess = word;
        await this.sendGuess();
      }
    },
    async sendGuess() {
      const guess = this.guess.toUpperCase();

      if (guess.length != 5) {
        this.errorMessage = "Please enter a 5-letter word";
        return;
      }

      if (guess.match(/(.).*\1/)) {
        this.errorMessage = "Your guess mustn't contain duplicate letters";
        return;
      }

      if (!guess.match(/^[A-Z]{5}$/)) {
        this.errorMessage = "Please use only the letters A through Z";
        return;
      }

      this.errorMessage = null;

      this.items.push({text: guess});

      const request = {guess: guess, state: this.state};
      const response = await axios.post('http://localhost:8081', request);
      
      const data = response.data;

      console.log("received response: ", data);

      this.state = data.state;
      this.items.pop();
      this.items.push({text: guess, hints: data.state.hints});

      if (guess == data.hiddenWord) {
        this.items.push({text: 'Congratulations! You guessed the word!'});
        this.done = true;
      }

      this.guess = null;
      this.focusInput("guess");
    },
    color(item, charIndex) {

      if (!(item.hints)) {
        return {};
      }

      const hint = item.hints[item.text[charIndex]]

      if (hint['no']) {
        return {no: true};
      } else if (hint['yes']) {
        return {yes: true};
      } else if (hint['elsewhere']) {
        return {elsewhere: true};
      } else {
        return {error: true};
      }
    },
    reset() {
      this.items = [];
      this.guess = null;
      this.state = this.defaultState;
      this.done = false;
    },
    focusInput: function ( inputRef ) {
      this.$refs[inputRef].focus();
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
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
ul {
  font-weight: 700;
  font-size: x-large;
  list-style: none;
}
</style>
