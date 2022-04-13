<template>
<div>
  <div class="mainBody">
    <div class="header">
      <h1 class="title">It is Wharrgarbl!</h1>
      <h3>Please enter your guess below</h3>
    </div>
    <div class="container">
      <div class="guessView">
        <div class="guessField">
          <input class="guess" ref="guess" v-model="guess" placeholder="Your guess here...">
          <button class="submit" @click="sendGuess()">Guess!</button>
          <button v-if="turnCount >= 3" @click="cheat = !cheat">I am a cheat and I want to see the secret word</button>
          <button v-if="done" @click="reset()">Play another game</button>
        </div>
        <ul class="latestGuess">
          <li v-for="(item, index) in recentItems" :key="index">
            <word-row :word="item.text" :hints="item.hints"></word-row>
          </li>
        </ul>
      </div>
      <history-view :items="items" />
    </div>
  </div>
  <div class="footer">
    <p>© 2022 Walden Creations - Privacy</p>
  </div>
  <div v-if="!done">
    <p v-if="cheat">The secret word is {{hiddenWord}}<span class="yes"></span>. Did knowing that make you happy?</p>
    <p v-if="errorMessage" class="error">{{errorMessage}}</p>
  </div>
</div>

</template>

<script>
import axios from 'axios';
import WordRow from './WordRow.vue';
import HistoryView from './HistoryView.vue';

export default {
  name: 'WharrgarblGame',
  components: {WordRow, HistoryView},
  data() {
    return {
      lang: 'en',
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

      const request = {guess: guess, state: this.state, lang: this.lang};
      console.log('sending', request);
      const data = await this.query(request);
      console.log('received', data);

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
        let host = process.env.VUE_APP_HOST ?? 'https://wharrapi.herokuapp.com';
        console.log('host', host);
        const response = await axios.post(`${host}/`, request);

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
  computed: {
    recentItems() {
      return this.items.slice(-6);
    }
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
