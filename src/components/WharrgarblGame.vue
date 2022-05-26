<template>
<div>
  <div class="mainBody">
    <div class="header">
      <h1 class="title">Welcome to Kinder Words!</h1>
      <h3>Please enter your guess below</h3>
    </div>
    <alphabet-list :hints="combinedHints"></alphabet-list>
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
      <history-view :items="items"></history-view>
    </div>
  </div>
  <div class="footer">
    <p>Privacy Policy: this application does not store or gather any kind of user information</p>
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
import AlphabetList from './AlphabetList.vue';

export default {
  name: 'WharrgarblGame',
  props: ["lang"],
  components: { WordRow, HistoryView, MessageBox, AlphabetList },
  data() {
    return {
      guess: '',
      done: false,
      errorMessage: null,
      working: false,
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
      this.working = true;
      console.log('sending', request);
      const data = await this.query(request);
      this.working = false;
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
    },
    combinedHints() {
      const hints = {};

      for (const item of this.items) {
        if (!item.hints) {
          continue;
        }

        for (const letter of Object.keys(item.hints)) {
          if (hints[letter]) {
            if (!(hints[letter].yes || hints[letter].no)) {
              hints[letter] = item.hints[letter];
            }
          } else {
            hints[letter] = item.hints[letter];
          }
        }
      }

      return hints;
    },
  },

  watch: {
    lang() {
      this.reset();
    },

    working(val) {
      console.log('error', val ? "Please wait..." : "");
      
      this.errorMessage = val ? "Please wait..." : "";
    },
  }
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
