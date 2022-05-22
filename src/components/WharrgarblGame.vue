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
          <button v-if="turnCount >= 3" @click="showCheat()">I am a cheat and I want to see the secret word</button>
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

  <message-box
    :show="errorMessage"
    :text="errorMessage"
  ></message-box>
</div>

</template>

<script>
import axios from 'axios';
import WordRow from './WordRow.vue';
import HistoryView from './HistoryView.vue';
import MessageBox from './MessageBox.vue';

export default {
  name: 'WharrgarblGame',
  props: ["lang"],
  components: {WordRow, HistoryView, MessageBox},
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
        this.showMessage("Please enter a 5-letter word");
        return;
      }

      if (guess.match(/(.).*\1/)) {
        this.showMessage("Your guess mustn't contain duplicate letters");
        return;
      }

      if (!guess.match(/^[A-Z]{5}$/)) {
        this.showMessage("Please use only the letters A through Z");
        return;
      }

      this.errorMessage = null;

      this.items.push({text: guess});

      const request = {guess: guess, state: this.state, lang: this.lang};
      console.log('sending', request);
      const data = await this.query(request);
      console.log('received', data);

      if (!data) {
        return;
      }

      if (data.error) {
        this.showMessage(data.error);
        this.items.pop();
        return;
      }

      this.state = data.state;
      this.hiddenWord = data.hiddenWord;
      this.items.pop();
      this.items.push({text: guess, hints: data.state.hints});

      if (guess == data.hiddenWord) {
        this.showMessage('Congratulations! You guessed the word!');
        this.done = true;
      }

      this.turnCount += 1;
      this.guess = null;
      this.focusInput("guess");
    },
    async query(request) {
      try {
        const host = process.env.VUE_APP_HOST ?? 'https://wharrapi.herokuapp.com';
        const response = await axios.post(`${host}/`, request);

        return response.data;
      } catch (ex) {
        console.error(ex);
        this.showMessage("An error occurred contacting the host");
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
    showMessage(text) {
      this.errorMessage = text;
      setTimeout(() => {
        this.errorMessage = "";
      }, 2000);
    },
    showCheat() {
      this.showMessage(`The secret word is "${this.hiddenWord}". Did knowing that make you happy?`)
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
