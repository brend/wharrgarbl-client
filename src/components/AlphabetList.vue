<template>
  <ul class="alphabetList">
    <li v-for="(letter, index) of letters" :key="index">
      <letter-box :hints="hints" :letter="letter"></letter-box>
    </li>
  </ul>
</template>

<script>
import LetterBox from './LetterBox.vue';
export default {
  props: ["hints"],
  components: { LetterBox },
  data() {
    return {
      letters: "ABCDEFGHIJKLMNOPQRSTUVWXYZ".split(''),
    };
  },
}
</script>

<style>

</style>