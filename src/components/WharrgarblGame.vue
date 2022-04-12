<template>
  <div class="hello">
    <h1>It is Wharrgarbl!</h1>
    <p>The rules are the same as the famous game Wordle by Josh Wardle, with some changes:</p>
    <p><em>No duplicate letters allowed</em>, i.e. FLOAT is ok, FLEET is not</p>
    <p>You may <em>guess as often as you like</em></p>
    <p>Letters that do not appear in the word are <span class="no"><b>gray</b></span>, letters that do appear but are in the wrong place are <span class="elsewhere"><b>yellow</b></span>, and letters that are already in the correct place are <span class="yes"><b>green</b></span>.</p>
    <h3>Please enter your guess below</h3>
    <div v-if="!done">
      <input ref="guess" v-model="guess">
      <button @click="sendGuess()">Guess!</button>
      <button v-if="turnCount >= 3" @click="cheat = !cheat">I am a cheat and I want to see the secret word</button>
      <p v-if="cheat">The secret word is {{hiddenWord}}<span class="yes"></span>. Did knowing that make you happy?</p>
      <p v-if="errorMessage" class="error">{{errorMessage}}</p>
    </div>
    <div v-if="done">
      <button @click="reset()">Play another game</button>
    </div>

    <ul>
      <li v-for="(item, index) in items" :key="index">
        <word-row :word="item.text" :hints="item.hints"></word-row>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';
import WordRow from './WordRow.vue';

export default {
  name: 'WharrgarblGame',
  components: {WordRow},
  data() {
    return {
      guess: '',
      done: false,
      errorMessage: null,
      cheat: false,
      hiddenWord: '',
      turnCount: 0,
      items: [],
      state: {
        hiddenWord: null,
        hints: {},
      },
      defaultState: {
        hiddenWord: null,
        hints: {},
      },
    };
  },
  methods: {
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
      const data = await this.query(request);

      if (data.error) {
        this.errorMessage = data.error;
        this.items.pop();
        return;
      }

      this.state = data.state;
      this.hiddenWord = data.hiddenWord;
      this.items.pop();
      this.items.push({text: guess, hints: data.state.hints});

      if (guess == data.hiddenWord) {
        this.items.push({text: 'Congratulations! You guessed the word!'});
        this.done = true;
      }

      this.turnCount += 1;
      this.guess = null;
      this.focusInput("guess");
    },
    async query(request) {
      try {
        // const response = await axios.post('http://localhost:8080', request);
        //const response = await axios.post('http://192.168.0.192:8080', request);
        const response = await axios.post('https://wharrapi.herokuapp.com', request);

        return response.data;
      } catch (ex) {
        console.error('oh boy', ex);
        return null;
      }
    },
    reset() {
      this.items = [];
      this.guess = null;
      this.state = this.defaultState;
      this.done = false;
      this.turnCount = 0;
    },
    focusInput: function ( inputRef ) {
      this.$refs[inputRef].focus();
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
ul {
  font-weight: 700;
  font-size: x-large;
  list-style: none;
}
</style>
